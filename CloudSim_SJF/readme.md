package org.cloudbus.cloudsim.examples;

import java.text.DecimalFormat;
import java.util.ArrayList;
import java.util.Calendar;
import java.util.Collections;
import java.util.Comparator;
import java.util.LinkedList;
import java.util.List;

import org.cloudbus.cloudsim.Cloudlet;
import org.cloudbus.cloudsim.CloudletSchedulerTimeShared;
import org.cloudbus.cloudsim.Datacenter;
import org.cloudbus.cloudsim.DatacenterBroker;
import org.cloudbus.cloudsim.DatacenterCharacteristics;
import org.cloudbus.cloudsim.Host;
import org.cloudbus.cloudsim.Log;
import org.cloudbus.cloudsim.Pe;
import org.cloudbus.cloudsim.Storage;
import org.cloudbus.cloudsim.UtilizationModel;
import org.cloudbus.cloudsim.UtilizationModelFull;
import org.cloudbus.cloudsim.Vm;
import org.cloudbus.cloudsim.VmAllocationPolicySimple;
import org.cloudbus.cloudsim.VmSchedulerTimeShared;
import org.cloudbus.cloudsim.core.CloudSim;
import org.cloudbus.cloudsim.provisioners.BwProvisionerSimple;
import org.cloudbus.cloudsim.provisioners.PeProvisionerSimple;
import org.cloudbus.cloudsim.provisioners.RamProvisionerSimple;

public class CloudSimSJF {

    private static List<Cloudlet> cloudletList;

    private static List<Vm> vmlist;

    public static void main(String[] args) {

        Log.printLine("Starting CloudSim SJF Simulation...");

        try {

            int num_user = 1;

            Calendar calendar = Calendar.getInstance();

            boolean trace_flag = false;

            CloudSim.init(num_user, calendar, trace_flag);

            Datacenter datacenter0 =
                    createDatacenter("Datacenter_0");

            DatacenterBroker broker =
                    createBroker();

            int brokerId = broker.getId();

            /*
             * Create VM
             */

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

            /*
             * Create Cloudlets
             */

            cloudletList =
                    new ArrayList<Cloudlet>();

            UtilizationModel utilizationModel =
                    new UtilizationModelFull();

            long[] cloudletLength = {
                    40000,
                    10000,
                    30000,
                    20000
            };

            for (int i = 0;
                 i < cloudletLength.length;
                 i++) {

                Cloudlet cloudlet =
                        new Cloudlet(
                                i,
                                cloudletLength[i],
                                1,
                                300,
                                300,
                                utilizationModel,
                                utilizationModel,
                                utilizationModel);

                cloudlet.setUserId(brokerId);

                cloudletList.add(cloudlet);
            }

            /*
             * Sort Cloudlets using SJF
             */

            Collections.sort(
                    cloudletList,
                    new Comparator<Cloudlet>() {

                @Override
                public int compare(
                        Cloudlet c1,
                        Cloudlet c2) {

                    return Long.compare(
                            c1.getCloudletLength(),
                            c2.getCloudletLength());
                }
            });

            broker.submitCloudletList(cloudletList);

            /*
             * Bind Cloudlets to VM
             */

            for (Cloudlet cloudlet : cloudletList) {

                broker.bindCloudletToVm(
                        cloudlet.getCloudletId(),
                        vm.getId());
            }

            /*
             * Start Simulation
             */

            CloudSim.startSimulation();

            List<Cloudlet> newList =
                    broker.getCloudletReceivedList();

            CloudSim.stopSimulation();

            printCloudletList(newList);

            Log.printLine(
                    "CloudSim SJF Finished!");

        }

        catch (Exception e) {

            e.printStackTrace();

            Log.printLine(
                    "Simulation terminated due to error");
        }
    }

    private static Datacenter createDatacenter(
            String name) {

        List<Host> hostList =
                new ArrayList<Host>();

        List<Pe> peList =
                new ArrayList<Pe>();

        peList.add(
                new Pe(
                        0,
                        new PeProvisionerSimple(1000)));

        hostList.add(
                new Host(
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
                    new VmAllocationPolicySimple(
                            hostList),
                    new LinkedList<Storage>(),
                    0);

        }

        catch (Exception e) {

            e.printStackTrace();
        }

        return datacenter;
    }

    private static DatacenterBroker createBroker() {

        DatacenterBroker broker = null;

        try {

            broker =
                    new DatacenterBroker("Broker");

        }

        catch (Exception e) {

            e.printStackTrace();

            return null;
        }

        return broker;
    }

    private static void printCloudletList(
            List<Cloudlet> list) {

        String indent = "    ";

        Log.printLine();

        Log.printLine(
                "========== OUTPUT ==========");

        Log.printLine(
                "Cloudlet ID"
                + indent
                + "STATUS"
                + indent
                + "VM ID"
                + indent
                + "Time"
                + indent
                + "Start Time"
                + indent
                + "Finish Time");

        DecimalFormat dft =
                new DecimalFormat("###.##");

        for (Cloudlet cloudlet : list) {

            Log.print(
                    indent
                    + cloudlet.getCloudletId()
                    + indent
                    + indent);

            if (cloudlet.getCloudletStatus()
                    == Cloudlet.SUCCESS) {

                Log.print("SUCCESS");

                Log.printLine(
                        indent
                        + indent
                        + cloudlet.getVmId()
                        + indent
                        + indent
                        + dft.format(
                                cloudlet.getActualCPUTime())
                        + indent
                        + indent
                        + dft.format(
                                cloudlet.getExecStartTime())
                        + indent
                        + indent
                        + dft.format(
                                cloudlet.getFinishTime()));
            }
        }
    }
}
