////////////////////////////////////////////////
//
//
//      CS 6378 - PROJECT 1
//   RICART AGRAWALA WITH OPTIMIZATION
//            CLIENT
//
//
/////////////////////////////////////////////////

This project has the  Ricart-Agrawala algorithm for distributed mutual exclusion, with
the optimization proposed by Roucairol and Carvalho, in a client-server model implementation.

Files Included
--------------
->client_program.cpp
->server_program.cpp

Process to compile
------------------
client:
-------

->Navigate to the program location.
-> type "g++ client_program.cpp -o client.out -lpthread -std=c++11"
->./client.out
->(run the client.out in different client machines given in the header as CLIENT0-4)

server:
-----
->Navigate to the program location.
-> type "g++ server_program.cpp -o server.out -lpthread -std=c++11"
->./server.out
->(run the client.out in different client machines given in the header as CLIENT0-4)


[Note:If running in different environment,change the directory path in the server]


MAKE CONNECTION:
----------------
->The CLIENT0 waits for the user input "1" to start the connection
->It triggers the remaining connection to all Servers and Clients
-> CLIENT0 connects to all servers and CLIENT1,2,3,4
-> When CLIENT1 receives the connection from O, it connects to remaining servers and CLIENT2,3,4 and so on.
->The connection is triggered till CLIENT4 connects to all server (doesnt connect to any client because it will have receiving connection form the remaining clients
-> Server waits for all the incoming connecction.

RICART AGRAWALA Algorithm with Optimization:
--------------------------------------------
The ricart agrawala optimization algorithm is implemented as per the pseudo code in paper 3-On mutual exclusion in computer networks.

Working
-------
The Client sends the ENQUIRE message to any server to get the files in the directory
Followed by continuous - READ/WRITE to Critical Section Files - File1.txt,File2.txt,File3.txt
->Finally type "exit" to close all the socket port and exit all threads


