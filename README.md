# CloudSim Experiment 1: Basic Datacenter and Cloudlet Simulation

This experiment demonstrates a basic CloudSim simulation where a single datacenter hosts one virtual machine (VM) that executes a single cloudlet. It is intended as an introductory example to understand CloudSim concepts such as datacenters, brokers, VMs, and cloudlets.

---

## 📌 Experiment Objective

- Create a datacenter with one host
- Deploy a single virtual machine (VM)
- Submit one cloudlet for execution
- Run the CloudSim simulation
- Display cloudlet execution results

---

## 🛠 Dependencies Required

To run this experiment, you need the following:

- **Java JDK** (Java 8 or above recommended)
- **CloudSim Toolkit** (version compatible with your code, e.g., CloudSim 3.x / CloudSim Plus as applicable)
- **Apache Maven** (optional, if managing dependencies via Maven)
- Any Java IDE (IntelliJ IDEA / Eclipse / VS Code)

---

## 📦 The Program Code

```java
package org.cloudbus.cloudsim.examples;

import org.cloudbus.cloudsim.*;
import org.cloudbus.cloudsim.core.CloudSim;
import org.cloudbus.cloudsim.provisioners.BwProvisionerSimple;
import org.cloudbus.cloudsim.provisioners.PeProvisionerSimple;
import org.cloudbus.cloudsim.provisioners.RamProvisionerSimple;

import java.text.DecimalFormat;
import java.util.ArrayList;
import java.util.Calendar;
import java.util.LinkedList;
import java.util.List;

public class CloudSimExample1 {

    public static DatacenterBroker broker;
    private static List<Cloudlet> cloudletList;
    private static List<Vm> vmlist;

    public static void main(String[] args) {
        Log.println("Starting CloudSimExample1...");

        try {
            int num_user = 1;
            Calendar calendar = Calendar.getInstance();
            boolean trace_flag = false;

            CloudSim.init(num_user, calendar, trace_flag);

            Datacenter datacenter0 = createDatacenter("Datacenter_0");

            broker = new DatacenterBroker("Broker");
            int brokerId = broker.getId();

            vmlist = new ArrayList<>();

            int vmid = 0;
            int mips = 1000;
            long size = 10000;
            int ram = 512;
            long bw = 1000;
            int pesNumber = 1;
            String vmm = "Xen";

            Vm vm = new Vm(
                    vmid,
                    brokerId,
                    mips,
                    pesNumber,
                    ram,
                    bw,
                    size,
                    vmm,
                    new CloudletSchedulerTimeShared()
            );

            vmlist.add(vm);
            broker.submitGuestList(vmlist);

            cloudletList = new ArrayList<>();

            int id = 0;
            long length = 400000;
            long fileSize = 300;
            long outputSize = 300;
            UtilizationModel utilizationModel = new UtilizationModelFull();

            Cloudlet cloudlet = new Cloudlet(
                    id,
                    length,
                    pesNumber,
                    fileSize,
                    outputSize,
                    utilizationModel,
                    utilizationModel,
                    utilizationModel
            );

            cloudlet.setUserId(brokerId);
            cloudlet.setGuestId(vmid);

            cloudletList.add(cloudlet);
            broker.submitCloudletList(cloudletList);

            CloudSim.startSimulation();
            CloudSim.stopSimulation();

            List<Cloudlet> newList = broker.getCloudletReceivedList();
            printCloudletList(newList);

            Log.println("CloudSimExample1 finished!");

        } catch (Exception e) {
            e.printStackTrace();
            Log.println("Unwanted errors happen");
        }
    }

    private static Datacenter createDatacenter(String name) {

        List<Host> hostList = new ArrayList<>();
        List<Pe> peList = new ArrayList<>();

        int mips = 1000;
        peList.add(new Pe(new PeProvisionerSimple(mips)));

        int ram = 2048;
        long storage = 1000000;
        int bw = 10000;

        hostList.add(
                new Host(
                        new RamProvisionerSimple(ram),
                        new BwProvisionerSimple(bw),
                        storage,
                        peList,
                        new VmSchedulerTimeShared(peList)
                )
        );

        String arch = "x86";
        String os = "Linux";
        String vmm = "Xen";
        double time_zone = 10.0;
        double cost = 3.0;
        double costPerMem = 0.05;
        double costPerStorage = 0.001;
        double costPerBw = 0.0;

        LinkedList<Storage> storageList = new LinkedList<>();

        DatacenterCharacteristics characteristics =
                new DatacenterCharacteristics(
                        arch,
                        os,
                        vmm,
                        hostList,
                        time_zone,
                        cost,
                        costPerMem,
                        costPerStorage,
                        costPerBw
                );

        Datacenter datacenter = null;

        try {
            datacenter = new Datacenter(
                    name,
                    characteristics,
                    new VmAllocationPolicySimple(hostList),
                    storageList,
                    0
            );
        } catch (Exception e) {
            e.printStackTrace();
        }

        return datacenter;
    }

    private static void printCloudletList(List<Cloudlet> list) {

        String indent = "    ";
        Log.println();
        Log.println("========== OUTPUT ==========");
        Log.println(
                "Cloudlet ID" + indent +
                "STATUS" + indent +
                "Data center ID" + indent +
                "VM ID" + indent +
                "Time" + indent +
                "Start Time" + indent +
                "Finish Time"
        );

        DecimalFormat dft = new DecimalFormat("###.##");

        for (Cloudlet cloudlet : list) {

            Log.print(indent + cloudlet.getCloudletId() + indent + indent);

            if (cloudlet.getStatus() == Cloudlet.CloudletStatus.SUCCESS) {

                Log.print("SUCCESS");

                Log.println(
                        indent + indent + cloudlet.getResourceId() +
                        indent + indent + indent + cloudlet.getGuestId() +
                        indent + indent + dft.format(cloudlet.getActualCPUTime()) +
                        indent + indent + dft.format(cloudlet.getExecStartTime()) +
                        indent + indent + dft.format(cloudlet.getExecFinishTime())
                );
            }
        }
    }
}

```

## ▶️ How to Run

1. Import the CloudSim library into your project  
2. Compile and run the `CloudSimExample1.java` file  
3. Observe the simulation logs and output in the console  

---

## 📷 Output Screenshot
<img width="716" height="314" alt="image" src="https://github.com/user-attachments/assets/ea5216a8-958f-4b5c-855f-fa5bdfd39e02" />


