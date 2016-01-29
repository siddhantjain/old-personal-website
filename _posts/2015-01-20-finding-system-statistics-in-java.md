---
layout: post
title: Finding System Statistics in Java
date: 2015-01-20 09:53
author: siddhantj
comments: true
categories: [Uncategorized]
---
Continuing on my quest to get more familiar with Java (and kill some time during this workless phase of the internship) I tried another problem to print some system statistics. The problem statement was to print the CPU utilisation, Disk Utilisation and Memory utilisation after every 2 seconds and print the average of these metrics affter ever minute. While this problem is quite straightforward in C/C++, it becomes a little interesting in Java because of JVM. The existing functions in Java would have all given the statistics for the virtual Machine and not for the actual system on which the program is running. A straight-forward google search suggested the use of  SIGAR  which basically made the task as simple as doing it C/C++. However, of course, it took away the flexibility of running the program on any machine as the binaries for the API were platform dependent. 

<strong>SIGAR</strong>

Being new to eclipse, I had to look up the steps to use the binaries from SIGAR and to add an external .jar file to the build path. just documenting those two things here, in case someone else gets stuck at that step as well. 

The steps are for Eclipse Luna.

I. Including an External .jar 


1.) Right click on the project file (in the project explorer)
2.) Select properties
3.) Select Java build path
4.) Click on "Add External jar"
5.) Select the jar file you want to add (in this case: SIGAR.jar)

II. Using a binary (.dll/.so file) in a project

1.) Copy-paste the .dll/.so file(s) in a new folder called dll (name could be anything)
2.) Go to Java build path as in the steps above
3.) Select the tab called "Source"
4.) Select Native libray location and click on edit
5.) Click on external folder and then browse through to select the dll folder we created in step 1


Once I had SIGAR in place, the rest of code was more or less simple. Appending the code here for reference: 


<strong>CODE</strong>
 

<blockquote><code>import java.io.*;
import java.util.*;
import java.text.*;
import java.lang.*;

import org.hyperic.sigar.CpuPerc;
import org.hyperic.sigar.CpuTimer;
import org.hyperic.sigar.DiskUsage;
import org.hyperic.sigar.FileSystemUsage;
import org.hyperic.sigar.Mem;
import org.hyperic.sigar.Sigar;
import org.hyperic.sigar.SigarException;




public class SystemMonitor {
	
	private static Sigar sigar = new Sigar();
	static double memSum=0;
	static double diskSum=0;
	
	private static int counter=0;
	static double CPUSum=0;
	
	public static void getSystemStatistics(){
		counter++;
		Mem mem = null;
		CpuPerc cpuperc = null;
		FileSystemUsage filesystemusage = null;
		try {
            mem = sigar.getMem();
            cpuperc = sigar.getCpuPerc();
            filesystemusage = sigar.getFileSystemUsage("C:");          
        } catch (SigarException se) {
            se.printStackTrace();
        }
		
		memSum = memSum+ mem.getUsedPercent();
		CPUSum = CPUSum + (cpuperc.getCombined()*100);
		diskSum = diskSum + filesystemusage.getUsePercent();
		
		System.out.print(mem.getUsedPercent()+"\t");
		System.out.print((cpuperc.getCombined()*100)+"\t");
		System.out.print(filesystemusage.getUsePercent()+"\n");
		
		if(counter==30){
			counter =0;
			System.out.print("*******************Average***************************\n");
			System.out.print(memSum/30+"\t");
			System.out.print(CPUSum/30+"\t");
			System.out.print(diskSum/30+"\n");
			System.out.print("******************************************************\n");
			memSum = 0;
			CPUSum = 0;
			diskSum = 0;
		}
	}

	public static void main(String[] args) {
		SystemMonitor m1 = new SystemMonitor();
		System.out.println("Memory Utilisation \t CPU Utilisation \t Disk Utilisation \n");
		while(true){
			try {
				Thread.sleep(2000);                 //1000 milliseconds is one second.
			} catch(InterruptedException ex) {
				Thread.currentThread().interrupt();
			}
		m1.getSystemStatistics();	
		
		}
	}
	

}
</code></blockquote>


