---
layout: post
title: Socket client-server program in Java
date: 2015-01-19 05:47
author: siddhantj
comments: true
categories: [client-server, EMC, java, Programming, socket programming]
---
A week ago I started my semester long internship in the Advanced Software Division at EMC, Bangalore. As a warm-up exercise (read: work to do till the projects are figured out), I was asked to write a basic socket client-server program in Java that allows multiple clients to connect to a server and execute the following commands:
<blockquote>chdir [dir]  //change directory to the given directory name
rmdir [dir] //remove directory
mkdir [dir] //make a new directory
list [ ] //list current working directory
exit //close the connection</blockquote>
I had written a simple one server-one client socket program in C for a lab exercise in a computer network courses at the university and almost began writing a similar program in Java, when I realised the slight complexity that connection of multiple clients at the same time brings to the problem statement.

<strong>Connecting multiple clients</strong>

For a single server-client program the task is simple for the server as at any point of time there will always be just one client it has to cater. However, when the server is talking to more than one client at a point of time it needs a separate interface for each one of them. What this calls for is the need of using a separate thread for each client. Each time a client logs out, only that thread is closed at the server side and the other threads continue working independently. Having a separate instance of the class running on an independent thread also helps us to segregate the variables. This becomes useful for keeping a track of the current working directory for each client.

<strong>Threads in Java</strong>

For writing the programs, I took help from various tutorials on the internet, sometimes picking up code snippets directly. As a result, my understanding of handling threads in Java is not very thorough. However, from what I understand, there is an existing <em>Thread</em> class in Java. A new class which needs to be running on a separate thread should be inherited from the Thread class. The existing <em>Thread</em> class has a <em>run()</em> method which should ideally be overridden with a run method for our inherited class. The <em>run()</em> method is called when the thread is started.

<strong>Structure of the programs</strong>

There are two different programs to be written. One for the server and one for the client (duh?). The client side program is fairly straightforward. We create a new socket for the connection and after taking the IP address of the server from the user, move on to creating the connection from the server. I guess an important aspect of client server programs is to understand the sequence of communication between the client and server. Before rushing on to writing the program (which I did), spend some time to frame the sequence of communication that your application needs. Mine goes something like this:

Server after accepting a connection from a client: *Sends welcome message.*  *Waiting for a command from the user*

Client on receiving welcome message: *Sends command and waits for server response*

Server: *Replies to command and waits for next command*

.

.

.

if client sends an exit command to server, the server first sends an exit message to client and then exits the thread on which the client was running. The client on the other hand waits for the exit response from the server after sending the exit message and then closes the connection. If you mess up in the sequence of steps for closing you can expect to get the "connection reset" error because the server/client will be trying to write/read from the socket where none exists.  There is also a very high chance of getting in a deadlock situation where the server and the client will both be waiting for a response from the other party before proceeding, if a proper sequence is not maintained.

I guess the codes can do the rest of the explaining for the structure of the programs, so I will append the codes I wrote for the client and the server program.

Client side

<code>package org.Exercise3.client;</code>

<code>import java.net.*;
import java.util.Scanner;
import java.io.*;</code>

<code>public class ClientSide {</code>

<code>public static void main(String [] args){
Scanner scan = new Scanner(System.in);
System.out.println("Enter IP address of computer to connect to: ");
String serverName = scan.nextLine();
int port = 12345;

System.out.println("Connecting to " + serverName + " at port " + port);
try {
Socket client = new Socket(serverName, port);
System.out.println("Just connected to " + client.getRemoteSocketAddress());
OutputStream writeToServer = client.getOutputStream();
DataOutputStream dataWriteToServer = new DataOutputStream(writeToServer);
InputStream readFromServer = client.getInputStream();
DataInputStream dataReadFromServer = new DataInputStream(readFromServer);
</code>
<code>System.out.println("Server Says: " + dataReadFromServer.readUTF());
System.out.println("Your Command?: ");
String userInput = scan.nextLine();</code>

<code>if (userInput.equals("logout") == true){
dataWriteToServer.writeUTF(userInput);
System.out.println("Server Says: " + dataReadFromServer.readUTF());
client.close();
System.exit(0);
}</code>

<code>while(userInput.equals("logout") == false){
System.out.println("Writing to server " + userInput);
dataWriteToServer.writeUTF(userInput);
System.out.println("Server Says: " + dataReadFromServer.readUTF());
System.out.println("Your command?: ");
userInput = scan.nextLine();
}</code>

<code>dataWriteToServer.writeUTF(userInput);
System.out.println("Server Says: " + dataReadFromServer.readUTF());
client.close();
} catch (IOException e) {
e.printStackTrace();
}</code>

<code>}
}
</code>
Server Side

<code><code>package org.Exercise3.server;</code></code>

import java.net.*;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.util.ArrayList;
import java.util.StringTokenizer;
import java.io.*;

public class DirectoryServer extends Thread {
private ServerSocket serverSocket;
private static final int SERVER_PORT = 12345;
private ArrayList connectedClients = new ArrayList();

<code>public DirectoryServer() throws IOException{
serverSocket = new ServerSocket(SERVER_PORT);
serverSocket.setSoTimeout(100000000);
}
</code>
<code>public void run(){
</code>
<code>while(true){
try{
System.out.println("Server has started.");
Socket server = serverSocket.accept();
connectedClients.add(new Client(server));
}catch(SocketTimeoutException s){
System.out.println("Socket timed out!");
return;
}catch(IOException e)
{
e.printStackTrace();
return;
}
}
}</code>

//Handles each client
<code>public class Client{
private Socket socket;
private IOport ioPort;</code>

<code>public Client(Socket newSocket){
System.out.println("new client created");
socket = newSocket;
ioPort = new IOport();
ioPort.start();
}</code>
<code>//handles all data for a client
</code>
<code>private class IOport extends Thread
{
private DataInputStream in;
private DataOutputStream out;
private String curDirectory = "C:\\";
public void run(){</code>

<code>//opening the input/output stream from/to the connected client
try
{
in = new DataInputStream(socket.getInputStream());
out = new DataOutputStream(socket.getOutputStream());
}
catch(IOException e)
{
System.out.println("Could not get input/output stream from a connected client");
return;
}</code>

<code>try{
//if an input stream is connected
System.out.println(socket + " has connected input/output");
out.writeUTF(" Welcome to the server. Enter a command: ");</code>

<code>String userCommand = in.readUTF();</code>

<code>if (userCommand.equals("logout")){
out.writeUTF("Logging you out");
socket.close();
return;
//TODO: Remove client from connected list
}</code>

<code>while(userCommand.equals("logout")==false){
String[] splitCommand = userCommand.split("\\s");
int opNumber = getOperationNumber(splitCommand[0]);
String directoryName = splitCommand[1];
switch (opNumber){
case 1:
//chdir
String newDirectoryName = directoryName;
Path path = Paths.get(newDirectoryName);
if(Files.exists(path)){
curDirectory = newDirectoryName;
out.writeUTF("Directory changed to" + newDirectoryName);
}
else{
out.writeUTF("Error in changing directory" + newDirectoryName);
}</code>

<code>break;</code>

<code>case 2:
//mkdir
File file = new File(curDirectory + "\\" + directoryName);
if (!file.exists()) {
if (file.mkdir()){
out.writeUTF("Directory with name " + directoryName + " created");
}
else {
out.writeUTF("failed to make directory");
}
}</code>

<code>else{
out.writeUTF("Directory with given name exists already");
}
</code>
<code>break;</code>

<code>case 3:
//list
File f = new File(curDirectory);
File[] files = f.listFiles();
String directoryContents = "Directory Contents: \n";
for (File eachFile : files) {
if (eachFile.isDirectory()) {
directoryContents = directoryContents + "\ndirectory: ";
}
</code>
<code>else {
directoryContents = directoryContents + "\nfile: ";
}
directoryContents = directoryContents + eachFile.getCanonicalPath();
}
out.writeUTF(directoryContents);
break;</code>

<code>case 4:
//rmdir
File dirToDel = new File(curDirectory + "//" + directoryName);
if(!dirToDel.exists()){
out.writeUTF("Directory to delete doesn't exist");
}</code>
<code>else{
delete(dirToDel);
out.writeUTF("Directory Deleted");
}
break;</code>

<code>default:
out.writeUTF("unidentified operation");
break;
}</code>

<code>userCommand = in.readUTF();
}//end of while</code>

<code>out.writeUTF("logging out: ");
socket.close();
return;
}catch(IOException e){
e.printStackTrace();
} //end of try/catch</code>

} //end of run

}//end of class IOport

<code>public int getOperationNumber(String OpWord){
int opCode = -1;
if(OpWord.equals("chdir")){opCode = 1;}
else if (OpWord.equals("mkdir")){opCode =2;}
else if (OpWord.equals("list")){opCode =3;}
else if (OpWord.equals("rmdir")){opCode = 4;}
return opCode;
}
}</code>

<code>private void delete(File dirToDel) {
// A recursive function to delete the entire contents of the directory</code>

<code>if(dirToDel.isDirectory()){</code>

//directory is empty, then delete it
if(dirToDel.list().length==0){

dirToDel.delete();

}else{

//list all the directory contents
String files[] = dirToDel.list();

for (String temp : files) {
//construct the file structure
File fileDelete = new File(dirToDel, temp);

//recursive delete
delete(fileDelete);
}

//check the directory again, if empty then delete it
if(dirToDel.list().length==0){
dirToDel.delete();
}
}

}else{
//if file, then delete it
dirToDel.delete();
}

}//end of class

public static void main(String [] args)
{
try
{
Thread t = new DirectoryServer();
t.start();
}catch(IOException e)
{
System.out.println("Error with Starting server itself");
e.printStackTrace();
}
}

}

Pardon the shabby formatting, I will (hopefully) edit it again with a better plugin for pasting code in wordpress.
