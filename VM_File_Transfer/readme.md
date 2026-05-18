commands and step to perform file transfer 

ifconfig

ip addr

touch file1.txt
touch file2.txt

echo "This is file 1 from VM1" > file1.txt
echo "This is file 2 from VM1" > file2.txt

cat file1.txt
cat file2.txt

scp file1.txt file2.txt user@192.168.1.11:/home/user/

cd /home/user

ls

cat file1.txt
cat file2.txt

echo "Text added in VM2" >> file1.txt
echo "Another line added in VM2" >> file2.txt

cat file1.txt
cat file2.txt

scp file1.txt user@192.168.1.10:/home/user/

scp file2.txt user@192.168.1.10:/home/user/
