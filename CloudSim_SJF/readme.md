package org.cloudbus.cloudsim.examples;

import java.text.DecimalFormat;
import java.util.*;

import org.cloudbus.cloudsim.*;
import org.cloudbus.cloudsim.core.CloudSim;
import org.cloudbus.cloudsim.provisioners.*;

public class CloudSimSJF {

    private static List<Cloudlet> cloudletList;
    private static List<Vm> vmlist;

    public static void main(String[] args) {

        Log.printLine("Starting SJF Simulation...");

        try {

            int num_user = 1;
            Calendar calendar = Calendar.getInstance();
            boolean trace_flag = false;

            CloudSim.init(num_user, calendar, trace_flag);

            Datacenter datacenter0 = createDatacenter("Datacenter_0");

            DatacenterBroker broker = createBroker();
            int brokerId = broker.getId();

            vmlist = new ArrayList<Vm>();

            Vm vm = new Vm(
                    0,
                    brokerId,
                    1000,
                    1,
                    512,
                    1000,
                    10000,
                    "Xen",
                    new CloudletSchedulerTimeShared());

            vmlist.add(vm);

            broker.submitVmList(vmlist);

            cloudletList = new ArrayList<Cloudlet>();

            UtilizationModel utilizationModel =
                    new UtilizationModelFull();

            long[] lengths = {40000, 20000, 30000, 10000};

            Arrays.sort(lengths);

            for (int i = 0; i < lengths.length; i++) {

                Cloudlet cloudlet = new Cloudlet(
                        i,
                        lengths[i],
                        1,
                        300,
                        300,
                        utilizationModel,
                        utilizationModel,
                        utilizationModel);

                cloudlet.setUserId(brokerId);
                cloudlet.setVmId(0);

                cloudletList.add(cloudlet);
            }

            broker.submitCloudletList(cloudletList);

            CloudSim.startSimulation();

            List<Cloudlet> newList =
                    broker.getCloudletReceivedList();

            CloudSim.stopSimulation();

            printCloudletList(newList);

            Log.printLine("SJF Simulation Finished!");

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static Datacenter createDatacenter(String name) {

        List<Host> hostList = new ArrayList<Host>();

        List<Pe> peList = new ArrayList<Pe>();

        peList.add(new Pe(0,
                new PeProvisionerSimple(1000)));

        hostList.add(new Host(
                0,
                new RamProvisionerSimple(2048),
                new BwProvisionerSimple(10000),
                1000000,
                peList,
                new VmSchedulerTimeShared(peList)));

        DatacenterCharacteristics characteristics =
                new DatacenterCharacteristics(
                        "x86",
                        "Linux",
                        "Xen",
                        hostList,
                        10.0,
                        3.0,
                        0.05,
                        0.001,
                        0.0);

        Datacenter datacenter = null;

        try {
            datacenter = new Datacenter(
                    name,
                    characteristics,
                    new VmAllocationPolicySimple(hostList),
                    new LinkedList<Storage>(),
                    0);

        } catch (Exception e) {
            e.printStackTrace();
        }

        return datacenter;
    }

    private static DatacenterBroker createBroker() {

        DatacenterBroker broker = null;

        try {
            broker = new DatacenterBroker("Broker");
        } catch (Exception e) {
            e.printStackTrace();
        }

        return broker;
    }

    private static void printCloudletList(List<Cloudlet> list) {

        DecimalFormat dft = new DecimalFormat("###.##");

        Log.printLine("\n========== OUTPUT ==========");

        Log.printLine("Cloudlet ID\tSTATUS\tVM ID\tTime");

        for (Cloudlet cloudlet : list) {

            Log.printLine(
                    cloudlet.getCloudletId() +
                    "\tSUCCESS\t" +
                    cloudlet.getVmId() +
                    "\t" +
                    dft.format(cloudlet.getActualCPUTime()));
        }
    }
}
