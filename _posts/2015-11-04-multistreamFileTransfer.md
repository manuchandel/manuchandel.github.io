---
layout: post
title: Multi Stream File Transfer Agent
author: Manu Chandel
category: project
repo : Multi-Stream-File-Transfer-Agent
excerpt : A file transfer program written in cpp, which uses multiple streams in parallel to transfer files by breaking it into equal fragments from one computer to another.<br>
---
This post is about Multi-Stream File Transfer agent. I wrote this program in my 5th semester, november 2015 as mini-network project. This project was successfully tested working on Ubuntu 14.04. Here I shall describe its working.<br>

GitHub repository for [Multi Stream File Transfer Agent](https://github.com/manuchandel/Multi-Stream-File-Transfer-Agent)

### Introduction

The file transfer agent consist of two sections receiver and sender.<br>

Sender (server) divides the file to be sent into equal fragments in a pre decided number of streams. <br>
Each stream (or connection ) sends one fragment independently (in parallel) to the receiver. <br>
Receiver (client) receives each file fragment independently (in parallel) through streams and creates temporary files (with `.temp` extension) for each fragment. <br>

Each temporary file contains a separate fragment which was received by receiver. <br>
Receiver performs merging of all temporary files sequentially to form original file. <br>
Temporary files are named in convention `F0.temp`, `F1.temp`, `F2.temp` ..... where `F0` represents first fragment received, `F1` represents second fragment received and so on..... <br>

The project makes use of following libraries

* \<thread\> library defined in C++11 standard is used for making streams i.e. multiple connections from receiver to sender.
* socket Interface defined in C lang is used to transfer bytes over the network.

The transfer over the network is carried by underlying TCP/IP protocol suite defined in socket interface. <br>

### Implementation

Different classes are defined in header files such as ..

* __ASSEMBLE_H.h__

[ `AssembleFile` ](https://github.com/manuchandel/Multi-Stream-File-Transfer-Agent/blob/master/ASSEMBLE_H.h) class provides service to receiver, to assemble fragments received and merge them into one file.<br>

* __DATA_TRANSFER_H.h__

[`DataTransfer`](https://github.com/manuchandel/Multi-Stream-File-Transfer-Agent/blob/master/DATA_TRANSFER_H.h) class provides service to sender, to read data for different fragments and transfer to receiver.<br>

* __FILE_TRANSFER_SERVER_H.h__

[`FileTransferServer`](https://github.com/manuchandel/Multi-Stream-File-Transfer-Agent/blob/master/FILE_TRANSFER_SERVER_H.h) class provides service to sender, to create server. <br>

* __RECEIVE_FRAGMENT_H.h__

[`ReceiveFragment`](https://github.com/manuchandel/Multi-Stream-File-Transfer-Agent/blob/master/RECEIVE_FRAGMENT_H.h) class provides service to receiver, to write received fragment bytes to temporary files.<br>

* __HEADERS_AND_CONSTANTS_H.h__

This file contains definition of [`FragmentHeader`](https://github.com/manuchandel/Multi-Stream-File-Transfer-Agent/blob/master/HEADERS_AND_CONSTANTS_H.h) structure, preprocessors required and constants declared throughout project.


>#### Sender's side

The link to sender's file can be found here [sender.cpp](https://github.com/manuchandel/Multi-Stream-File-Transfer-Agent/blob/master/sender.cpp)

1. __main(int argc,char *argv[])__
  * It creates object of `FileTransferServer` which initializes server socket .
  * Then it calls `connectClient()` function .

2. __connectClient(FileTransferServer *,char [])__
  * Divides file into different fragments and stores information about each fragment in `DataTransfer` object.
  * Listens and accepts connection on `PORT` and `HOST`.
  * As soon as client is connected, initiates transfer process and in parallel continues to accept connections until pre defined number of streams are created.

3. __sendFragment(int client,DataTransfer *)__
  * Sends `FragmentHeader` to receiver informing about file size, sequence number and file name extension.
  * After receiving acknowledgment from receiver starts sending data to receiver.
  * After the data is sent, sender waits for tear down packet from receiver and then closes connection.

>#### Receiver's side

The link to receiver's file can be found here [receiver.cpp](https://github.com/manuchandel/Multi-Stream-File-Transfer-Agent/blob/master/receiver.cpp)

1. __manageClient()__
  * Creates multiple threads (equal to stream number) and begins parallel processing by calling `connectClient()`.
  * `connectClient()` calls `readMessage()` which reads all bytes and stores fragment in `.filetransfer` directory (which is hidden in LINUX).
  * At last when control returns to this method it begins assembling of all fragments to output merged file.

2. __connectClient(ReceiveFragment *)__
 * This method is called by each thread independently (in parallel).
 * It creates a client socket and connects to server.
 * After connection is made `readMessage()` is called by each thread from `connectClient()` .

3. __readMessage(int client,ReceiveFragment *)__
 * This method is called by each thread separately.
 * It receives fragment of file from sender and creates its temporary file.
 * Initailly `FragmentHeader` is received and information such as fragment `size`, `part_no` is retrieved.
 * After the information is retrieved, a temporary file is created and named according to `part_no` as `F0.temp` or `F1.temp` or...
 * Acknowledgement is sent to sender indicating that receiver is ready to receive bytes.
 * Given `size` of bytes are read and then tear down packet is sent to sender indicating close of connection.

### Future work

As of now there is no mechanism to check integrity of transfered file but one way hash function such as `SHA-256` can be used.<br>

The file to be sent is divided into predefined equal fragments and same number of streams are created to sent it. This approach is not optimal. Transfer process can be speed up by designing an algorithm which takes input parameters such as..<br>

* File size.
* Bandwidth of connection.
* Processor configuration of sender & receiver. <br>

and decides the number of streams & fragments to be created.
