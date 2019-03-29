//Example code: A simple server side code, which echos back the received message. 
//Handle multiple socket connections with select and fd_set on Linux  
#include <stdio.h>  
#include <string.h>   //strlen  
#include <stdlib.h>  
#include <errno.h>  
#include <unistd.h>   //close  
#include <arpa/inet.h>    //close  
#include <sys/types.h>  
#include <sys/socket.h>  
#include <netinet/in.h>  
#include <sys/time.h> //FD_SET, FD_ISSET, FD_ZERO macros  
#include <iostream>
#include <typeinfo>
#define TRUE   1  
#define FALSE  0  
#define PORT 2408 
#include "connection.h" 
#include <set> 
#include <iterator>
using namespace std;  
int main()   
{   
    struct hostent *host;
    char *ClientIp[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};	
    int opt = 1;   
    int master_socket , addrlen , new_socket , client_socket[5],max_clients = 5 , activity, i , valread , sd;   
    int max_sd;   
    struct sockaddr_in address;   
       
    char buffer[1025];  //data buffer of 1K  
         
    //set of socket descriptors  
    fd_set readfds;   
         
    //a message  
    char *message = "ECHO Daemon v1.0 \r\n";   
   

    //initialise all client_socket[] to 0 so not checked  
    for (i = 0; i < max_clients; i++)   
    {   
        client_socket[i] = 0;   
    }   
         
    //create a master socket  
    if( (master_socket = socket(AF_INET , SOCK_STREAM , 0)) == 0)   
    {   
        perror("socket failed");   
        exit(EXIT_FAILURE);   
    }   
     
    //set master socket to allow multiple connections ,  
    //this is just a good habit, it will work without this  
    if( setsockopt(master_socket, SOL_SOCKET, SO_REUSEADDR, (char *)&opt,sizeof(opt)) < 0 )   
    {   
        perror("setsockopt");   
        exit(EXIT_FAILURE);   
    }   
     
    //type of socket created  
    address.sin_family = AF_INET;   
    address.sin_addr.s_addr = htonl(INADDR_ANY);   
    address.sin_port = htons( PORT );   
    //send connection
	sockaddr_in sendSockAddr;
	int clientSd = socket(AF_INET, SOCK_STREAM, 0);
	if( setsockopt(clientSd, SOL_SOCKET, SO_REUSEADDR, (char *)&opt,sizeof(opt)) < 0 )   
    	{   
        	perror("setsockopt");   
       	 	exit(EXIT_FAILURE);   
    	}
	for(int i=0;i<4;i++)
	{	
	   
	 
	sendSockAddr.sin_family = AF_INET; 
	sendSockAddr.sin_port=0;
	sendSockAddr.sin_addr.s_addr =inet_addr(ClientIp[i]);
	sendSockAddr.sin_port = htons(2408);
	int status = connect(clientSd,(sockaddr*) &sendSockAddr, sizeof(sendSockAddr));
	if(status < 0)
	{
		cout<<"not connected"<<endl;
	}
	}	
    //bind the socket to localhost port 8888  
    if (bind(master_socket, (struct sockaddr *)&address, sizeof(address))<0)   
    {   
        perror("bind failed");   
        exit(EXIT_FAILURE);   
    } 
	 //find the machine which I run on
	struct sockaddr* res=(struct sockaddr*)&address;
	struct sockaddr_in* addr_in = (struct sockaddr_in*)res;
	cout <<endl<< typeid(res).name() << endl;
	char *s = inet_ntoa((addr_in)->sin_addr);
	printf("IP address: %s\n", s);
  
    printf("Listener on port %d \n", PORT);   
         
    //try to specify maximum of 3 pending connections for the master socket  
    if (listen(master_socket, 5) < 0)   
    {   
        perror("listen");   
        exit(EXIT_FAILURE);   
    }   
         
    //accept the incoming connection  
    addrlen = sizeof(address);   
    puts("Waiting for connections ...");   
	set<string> connectedIP;          
	
    while(TRUE)   
    {   
	cout<<connectedIP.size()<<endl;
	if (connectedIP.size()>0)
        cout <<*connectedIP.begin() <<endl ;
	if(connectedIP.size()>3)
		break;
        //clear the socket set  
        FD_ZERO(&readfds);   
     
        //add master socket to set  
        FD_SET(master_socket, &readfds);   
        max_sd = master_socket;   
             
        //add child sockets to set  
        for ( i = 0 ; i < max_clients ; i++)   
        {   
            //socket descriptor  
            sd = client_socket[i];   
                 
            //if valid socket descriptor then add to read list  
            if(sd > 0)   
                FD_SET( sd , &readfds);   
                 
            //highest file descriptor number, need it for the select function  
            if(sd > max_sd)   
                max_sd = sd;   
        }   
     	//cout<<"waiting here for select level triggered"<<endl;
        //wait for an activity on one of the sockets , timeout is NULL ,  
        //so wait indefinitely  
        activity = select( max_sd + 1 , &readfds , NULL , NULL , NULL);   
       //cout<<"stop"<<endl;
        if ((activity < 0) && (errno!=EINTR))   
        {   
            printf("select error");   
        }   
             
        //If something happened on the master socket ,  
        //then its an incoming connection  
        if (FD_ISSET(master_socket, &readfds))   
        {   
		
            if ((new_socket = accept(master_socket,(struct sockaddr *)&address, (socklen_t*)&addrlen))<0)   
            {   
                perror("accept");   
                exit(EXIT_FAILURE);   
            }   
             
            //inform user of socket number - used in send and receive commands  
            printf("New connection , socket fd is %d , ip is : %s , port : %d\n" , new_socket , inet_ntoa(address.sin_addr) , ntohs(address.sin_port));   
           connectedIP.insert( inet_ntoa(address.sin_addr));
            //send new connection greeting message  
            if( send(new_socket, message, strlen(message), 0) != strlen(message) )   
            {   
                perror("send");   
            }   
                 
            puts("Welcome message sent successfully");   
                 
            //add new socket to array of sockets  
            for (i = 0; i < max_clients; i++)   
            {   
                //if position is empty  
                if( client_socket[i] == 0 )   
                {   
                    client_socket[i] = new_socket;   
                    printf("Adding to list of sockets as %d\n" , i);   
                         
                    break;   
                }   
            }   
        }

	}
     sendSockAddr.sin_addr.s_addr =INADDR_ANY; 
     int status = connect(clientSd,(sockaddr*) &sendSockAddr, sizeof(sendSockAddr));
     if(status>0)
     cout<<"connected all 5 clients"<<endl;
      
         
    return 0;   
}  




/*             
        //else its some IO operation on some other socket 
        for (i = 0; i < max_clients; i++)   
        {   
            sd = client_socket[i];   
                 
            if (FD_ISSET( sd , &readfds))   
            {   
                //Check if it was for closing , and also read the  
                //incoming message  
                if ((valread = read( sd , buffer, 1024)) == 0)   
                {   
                    //Somebody disconnected , get his details and print  
                    getpeername(sd , (struct sockaddr*)&address , \ 
                        (socklen_t*)&addrlen);   
                    printf("Host disconnected , ip %s , port %d \n" ,  
                          inet_ntoa(address.sin_addr) , ntohs(address.sin_port));   
                         
                    //Close the socket and mark as 0 in list for reuse  
                    close( sd );   
                    client_socket[i] = 0;   
                }   
                     
                //Echo back the message that came in  
                else 
                {   
                    //set the string terminating NULL byte on the end  
                    //of the data read  
                    buffer[valread] = '\0';   
                    send(sd , buffer , strlen(buffer) , 0 );   
                }   
            }   
        } 
*/ 






//////////////////////////////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////////////////////////


#include <ifaddrs.h>
#include "connection.h"
using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0

string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	char buffer[1024] = {0}; 
	char *hello = "Hello"; 

	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}

	int connect_socket(char* IPname)
	{	
		address.sin_addr.s_addr = inet_addr(IPname);
		//cout<<address.sin_addr.s_addr<<endl;
		if (connect(socket_fd, (struct sockaddr *)&address, sizeof(address)) < 0) 
    		{ 
        		printf("\nConnection Failed \n"); 
        		return 0; 
    		} 
    		return 1;
	}
	int listen_socket()
	{	
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, SO_REUSEADDR | SO_REUSEPORT,&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = htonl(INADDR_ANY); 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		cout<<"waiting to connect here"<<endl;
		if (listen(socket_fd, 10) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		return 1;
	}
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	SocketWrapper()
	{
		sender="";
		receiver="";
	}
	
	Socket_connection* socket_id=new Socket_connection();
	
};
int makeConnection(list <SocketWrapper*> SocketConnectionList)//
{	
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//Socket_connection s1;// for the listening clients
	//Socket_connection* s1=new Socket_connection();
	int connection_start,status,client_num,flag=0;
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	if (getIPAddress()==CLIENT0)
	{	
		cout<<"Enter 1 to setup connection: ";
		cin>>connection_start;
		if (connection_start==1)
		{	
			for(int i=1;i<5;i++)
			{	
				Socket_connection* s1=new Socket_connection();  //created for the sender clients
				SocketWrapper* w1=new SocketWrapper();
				w1->sender=CLIENT0;
				w1->receiver=clientIPList[i];
				
				status=s1->connect_socket(clientIPList[i]);
				if(status==0)
				{
					cout<<"error in connection - sender side"<<endl;
					
					return 0;
				}
				else
					cout<<"Sender - connected "<< w1->sender <<" to "<< w1->receiver <<endl;
				
				w1->socket_id=s1;
				SocketConnectionList.push_back(w1);
				
			}		
					
		}
		else
		{
			cout<<"exiting...";
			return 0;
		}
	}
	else
	{
		
		
		for(int i=0;i<5;i++)// to change the client number chage here to ...
		{
			if(getIPAddress()==string(clientIPList[i]))
			{
				client_num=i;
				break;	
			}
		}
		while(1)
		{	
			
			Socket_connection* s1=new Socket_connection();	
					
			int stat=s1->listen_socket();
			if (stat==1) 
			{	
								
				pid_t pid = fork();
				if (pid==0)//only child process
				{
					SocketWrapper* w1=new SocketWrapper();
					w1->sender=inet_ntoa(s1->address.sin_addr);
					w1->receiver=getIPAddress();
					w1->socket_id=s1;
					SocketConnectionList.push_back(w1);
					cout<< "Receiver - connected "<< w1->sender <<" to "<< w1->receiver <<endl;
				
					if (flag==0)
					{					
						
					
						for(int i=client_num+1;i<5;i++)
						{
							sleep(2);
							Socket_connection* s1=new Socket_connection();  //created for the sender clients
							SocketWrapper* w1=new SocketWrapper();
							w1->sender=getIPAddress();
							w1->receiver=clientIPList[i];
							//connecting the clients in mesh
							int stat=s1->connect_socket(clientIPList[i]);
							w1->socket_id=s1;
							if (stat==1)
							{
								SocketConnectionList.push_back(w1);
								cout<<"Sender - connected "<< w1->sender <<" to "<< w1->receiver <<endl;
							}
							else
							{
								cout<<"error.."<<endl;
								return 0;
							}					
						}
					}	
					return -1;
				}
				flag=1;
			}
			else
			{	
				cout<<"couldnt connect to the socket-receiver side"<<endl;
				return 0;
			}
			
			
		}
	}
	
	return 1;
}

int main()
{	
	list <SocketWrapper*> SocketConnectionList; 
	int status=makeConnection(SocketConnectionList);//
	if (status==0)
	{
		cout<<"Problem with the connection... Please check the Client connection..."<<endl;
		exit(1);	
	}
	else if(status==1)
	{
		cout<<"Client Connection Established......."<<endl;
		
		list <SocketWrapper*> :: iterator it; 
    		for(it = SocketConnectionList.begin(); it != SocketConnectionList.end(); ++it) 
		{
        		cout << "conection from" << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
    		}
	}
	else
	{
		cout<<"exit child"<<endl;
	}
		
	return 0;
}












//////////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////////////////
#include <ifaddrs.h>
#include "connection.h"
using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
 
string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	char buffer[1024] = {0}; 
	char *hello = "Hello"; 

	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}

	int connect_socket(char* IPname)
	{	
		address.sin_addr.s_addr = inet_addr(IPname);
		//cout<<address.sin_addr.s_addr<<endl;
		if (connect(socket_fd, (struct sockaddr *)&address, sizeof(address)) < 0) 
    		{ 
        		printf("\nConnection Failed \n"); 
        		return 0; 
    		} 
    		return 1;
	}
	int listen_socket()
	{	
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, SO_REUSEADDR | SO_REUSEPORT,&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = htonl(INADDR_ANY); 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		cout<<"waiting to connect here"<<endl;
		if (listen(socket_fd, 10) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		return 1;
	}

			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	SocketWrapper()
	{
		sender="";
		receiver="";
	}
	
	Socket_connection* socket_id=new Socket_connection();
	
};
struct thread_data 
{
	
	list <SocketWrapper*> SocketConnectionList;
};
struct Client_thread_data 
{
	
	list <SocketWrapper*> SocketConnectionList;
	Socket_connection* socket_id=new Socket_connection();
	string sender;
	string receiver;
	
};
struct Client_send_data
{
	int client_num;
	list <SocketWrapper*> SocketConnectionList;
	int over_ride;
};
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};	
	cout<<"send connection thread"<<endl;
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char buffer[1024] = {0};
	char *setup_msg = "start";
	int valread=0;
	if (data->over_ride==0)
	{
		cout<<"wait for before client to connect and send data"<<endl;
		
		list <SocketWrapper*> :: iterator it; 
		for(it = (data->SocketConnectionList).begin(); it != (data->SocketConnectionList).end(); ++it) 
		{
			//cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
			if((*it)->sender==clientIPList[(data->client_num)-1] && (*it)->receiver==clientIPList[data->client_num])
			{
				cout<<"Setup message received from "<< clientIPList[(data->client_num)-1];
				//valread = read((*it)->socket_id  , buffer, 1024); 
    				if(valread)
					cout<<buffer<<endl<<" #### "<<endl; 
				break;
			}
		}
	    		
	}
	if (valread or data->over_ride)	
	for(int i=(data->client_num)+1;i<5;i++)
	{
	
		Socket_connection* s1=new Socket_connection();  //created for the sender clients
		SocketWrapper* w1=new SocketWrapper();
		w1->sender=getIPAddress();
		w1->receiver=clientIPList[i];
		//connecting the clients in mesh
		int stat=s1->connect_socket(clientIPList[i]);
		w1->socket_id=s1;
		if (stat==1)
		{
			data->SocketConnectionList.push_back(w1); //need mutex here
			cout<<"Sender - connected "<< w1->sender <<" to "<< w1->receiver <<endl;
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
			
		}
		
		/*
		list <SocketWrapper*> :: iterator it; 
		for(it = (data->SocketConnectionList).begin(); it != (data->SocketConnectionList).end(); ++it) 
		{
			cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
		}
		*/
	
	}
	list <SocketWrapper*> :: iterator it; 
	for(it = (data->SocketConnectionList).begin(); it != (data->SocketConnectionList).end(); ++it) 
	{
		//cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
		if((*it)->sender==clientIPList[data->client_num] && (*it)->receiver==clientIPList[(data->client_num)+1])
		{
			//send(&((*it)->socket_id) , setup_msg , strlen(setup_msg) , 0 ); 
			cout<<"Setup message sent to "<< clientIPList[(data->client_num)+1];
			break;
		}
	}
	
}
void *ClientConnect(void *threadarg)
{
	cout<<"Save Client Thread created"<<endl;	
	struct Client_thread_data *data;
   	data = (struct Client_thread_data *) threadarg;
	SocketWrapper* w1=new SocketWrapper();
	w1->sender=data->sender;
	w1->receiver=data->receiver;
	w1->socket_id=data->socket_id;
	//mtx.lock();	
	data->SocketConnectionList.push_back(w1);  //need mutex for this 
	//mtx.unlock();
	cout<<"Save Client Thread created"<<endl;
	
	list <SocketWrapper*> :: iterator it; 
	for(it = (data->SocketConnectionList).begin(); it != (data->SocketConnectionList).end(); ++it) 
	{
		cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
	}
	
}

void *makeConnection(void *threadarg)//initiate connection to all client
{	
	cout<<"Main Client Thread created"<<endl;
	struct thread_data *Client_data;
	Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	int connection_start,status,client_num,flag=0,rc;
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		if (connection_start==1)
		{	
			pthread_t SendConnectThread;
			struct Client_send_data td;
			td.SocketConnectionList=Client_data->SocketConnectionList;
			td.client_num=client_num;
			td.over_ride=1;
			int rc = pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&td);
			if (rc)
			{
				cout<<"problem in creating thread"<<endl;
				
			}	
					
		}
		else
		{
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			
		}
	}
	while(1)
	{	
		
		Socket_connection* s1=new Socket_connection();	
		int stat=s1->listen_socket();
		if (stat==1) 
		{	
			//listen connect store
			pthread_t ClientSocketStore;
			struct Client_thread_data td;
			td.SocketConnectionList=Client_data->SocketConnectionList;
			td.socket_id=s1;
			td.sender=inet_ntoa(s1->address.sin_addr);
			td.receiver=getIPAddress();
			rc = pthread_create(&ClientSocketStore, NULL, ClientConnect, (void *)&td );
			if(rc)
			{
				cout<<"problem in thread receiver - store"<<endl;
				
			}
			pthread_t SendConnectThread; 
			struct Client_send_data t;
			t.SocketConnectionList=Client_data->SocketConnectionList;
			t.client_num=client_num;
			t.over_ride=0;
			
			rc = pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
			if (rc)
			{
				cout<<"problem in creating thread"<<endl;
			}					
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
			
		}
		
	}	
	
}


int main()
{	
	pthread_t ClientThread;
	//pthread_attr_t attr;
	//pthread_attr_init(&attr);
   	//pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);
	list <SocketWrapper*> SocketConnectionList; 
	int rc;		
	struct thread_data td;
	td.SocketConnectionList=SocketConnectionList;
	rc = pthread_create(&ClientThread, NULL, makeConnection, (void *)&td);
	
	//int status=makeConnection(SocketConnectionList);//
	
	if (rc)
	{
		cout<<"Problem with the creating client connection thread... Please check the Client connection..."<<endl;
		exit(1);	
	}
	else 
	{
		cout<<"Client Connection thread Established......."<<endl;
		
	}
	while(1)
	{
	}
	
		
	return 0;
}


//////////////////////////////





#include <ifaddrs.h>
#include "connection.h"
using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
//pthread_mutex_t client_lock;


string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	char buffer[1024] = {0}; 
	char *hello = "Hello"; 

	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}

	int connect_socket(char* IPname)
	{	
		address.sin_addr.s_addr = inet_addr(IPname);
		//cout<<address.sin_addr.s_addr<<endl;
		if (connect(socket_fd, (struct sockaddr *)&address, sizeof(address)) < 0) 
    		{ 
        		printf("\nConnection Failed \n"); 
        		return 0; 
    		} 
    		return 1;
	}
	int listen_socket()
	{	
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, SO_REUSEADDR | SO_REUSEPORT,&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = htonl(INADDR_ANY); 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		cout<<"waiting to connect here"<<endl;
		if (listen(socket_fd, 10) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		return 1;
	}

			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	SocketWrapper()
	{
		sender="";
		receiver="";
	}
	
	Socket_connection* socket_id=new Socket_connection();
	
};
struct thread_data 
{
	
	list <SocketWrapper*> *SocketConnectionList;
	list <sem_t*> *client_lock;
	
};
struct Client_thread_data 
{
	
	list <SocketWrapper*> *SocketConnectionList;
	Socket_connection* socket_id=new Socket_connection();
	string sender;
	string receiver;
	
};
struct Client_send_data
{
	int client_num;
	list <SocketWrapper*> *SocketConnectionList;
	int over_ride;
	list <sem_t*> *client_lock;
	
};
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char buffer[1024] = {0};
	char *setup_msg = "start";
	int valread=0;
	cout<<"Addr at makeconn:"<<data->client_lock<<endl;


	int i2=0;	
	for(list <sem_t*> :: iterator it = (data->client_lock)->begin(); it != (data->client_lock)->end(); ++it,i2++) 
	{	
		if(i2==data->client_num)
			sem_wait(*it);
		int value;
		sem_getvalue(*(it), &value);
		cout<<"state..."<<" "<<value<<endl;
	}


	//pthread_mutex_lock(data->client_lock);
	if (data->over_ride==0)
	{
		int i1=0;
		for(list <sem_t*> :: iterator it = (data->client_lock)->begin(); it != (data->client_lock)->end(); ++it,i1++) 
		{	
			if(i1==(data->client_num)-1)
				sem_wait(*it);
			//int value;
			//sem_getvalue(*(it), &value);
			//cout<<"state..."<<" "<<value<<endl;
		}
		list <SocketWrapper*> :: iterator i;
		cout<<"wait for before client to connect and send data #"<<data->client_num<<endl;
		//sem_wait(&(data->client_lock)+(data->client_num));
		
		//sem_wait(data->client_lock->front());
		//pthread_mutex_lock(data->client_lock);
		//pthread_cond_wait(&client_cond[(data->client_num)],&client_lock[(data->client_num)]);
		cout<<"woke up"<<endl;
		for(i = (data->SocketConnectionList)->begin(); i != (data->SocketConnectionList)->end(); ++i) 
		{	
			if((*i)->sender==clientIPList[(data->client_num)-1] && (*i)->receiver==clientIPList[data->client_num])
			{
				cout<<"Setup message received from "<< clientIPList[(data->client_num)-1];
				valread = read(((*i)->socket_id)->socket_fd  , buffer, 1024); 
				cout<<buffer[0]<<endl<<" #### "<<endl;
    				if(valread && (string(buffer)=="start"))
					cout<<string(buffer)<<endl<<" #### "<<endl; 
				break;
			}	
			
		}
		
	    		
	}
	if (valread or data->over_ride)	
	for(int i=(data->client_num)+1;i<5;i++)
	{
		
		Socket_connection* s1=new Socket_connection();  //created for the sender clients
		SocketWrapper* w1=new SocketWrapper();
		w1->sender=getIPAddress();
		w1->receiver=clientIPList[i];
		//connecting the clients in mesh
		int stat=s1->connect_socket(clientIPList[i]);
		w1->socket_id=s1;
		if (stat==1)
		{	
			pthread_mutex_lock(&list_lock);
			data->SocketConnectionList->push_front(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - connected "<< w1->sender <<" to "<< w1->receiver <<endl;
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
			
		}
		
		/*
		list <SocketWrapper*> :: iterator it; 
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
		}
		*/
	
	}
	
	
	list <SocketWrapper*> :: iterator it;	
	for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
	{
		//cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
		
		if((*it)->sender==clientIPList[data->client_num] && (*it)->receiver==clientIPList[(data->client_num)+1])
		{
					
			send(((*it)->socket_id)->socket_fd , setup_msg , strlen(setup_msg) , 0 ); 
			cout<<"Setup message sent to "<< clientIPList[(data->client_num)+1]<<endl;
			break;
		}
		
	}
	
	//sem_post(&client_lock[data->client_num]);
	//sem_post(data->client_lock->front());
	
	int i1=0;	
	for(list <sem_t*> :: iterator it = (data->client_lock)->begin(); it != (data->client_lock)->end(); ++it,i1++) 
	{	
		if(i1==data->client_num)
		{
			sem_post(*it);
			cout<<"*";
		}
		int value;
		sem_getvalue(*(it), &value);
		cout<<"state..."<<" "<<value<<endl;
	}	
	//pthread_cond_signal(&client_cond[(data->client_num)]);
	//pthread_mutex_unlock(data->client_lock);
	//int value;	
	//for(int i=0;i<5;i++)
	//{
	//	sem_getvalue(&data->client_lock[i], &value);
	//	cout<<"state..."<<i<<" "<<value<<endl;
	//}
	cout<<"list ended.."<<endl;
	
}
/*
void *ClientConnect(void *threadarg)
{
	cout<<"Save Client Thread created"<<endl;	
	struct Client_thread_data *data;
   	data = (struct Client_thread_data *) threadarg;
	SocketWrapper* w1=new SocketWrapper();
	
	cout<<	data->sender<<data->receiver;
	w1->sender=data->sender;
	w1->receiver=data->receiver;
	cout<<"Save Client saved"<<endl;
	w1->socket_id=data->socket_id;

	//mtx.lock();	
	data->SocketConnectionList->push_back(w1);  //need mutex for this 
	//mtx.unlock();
	cout<<"Save Client saved"<<endl;
	
	list <SocketWrapper*> :: iterator it; 
	for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
	{
		cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
	}
	
}
*/
void *makeConnection(void *threadarg)//initiate connection to all client
{	
	cout<<"Main Client Thread created"<<endl;
	struct thread_data *Client_data;
	Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	int connection_start,status,client_num,flag=0,rc;
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	cout<<"Addr at makeconn:"<<Client_data->client_lock<<endl;
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	/*
	int value;
	list <sem_t*> :: iterator it;	
	for(it = (Client_data->client_lock)->begin(); it != (Client_data->client_lock)->end(); ++it) 
	{	
		sem_getvalue(*(it), &value);
		cout<<"state..."<<" "<<value<<endl;
	}
	*/
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		if (connection_start==1)
		{	
			pthread_t SendConnectThread;
			struct Client_send_data td;
			td.client_lock=Client_data->client_lock;
			
			td.SocketConnectionList=Client_data->SocketConnectionList;
			td.client_num=client_num;
			td.over_ride=1;
			int rc = pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&td);
			if (rc)
			{
				cout<<"problem in creating thread"<<endl;
				
			}	
					
		}
		else
		{
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			
		}
	}
	while(1)
	{	
		
		Socket_connection* s1=new Socket_connection();	
		int stat=s1->listen_socket();
		if (stat==1) 
		{	
			SocketWrapper* w1=new SocketWrapper();			//need mutex
			w1->sender=inet_ntoa(s1->address.sin_addr);
			w1->receiver=getIPAddress();
			w1->socket_id=s1;
			pthread_mutex_lock(&list_lock);
			(Client_data->SocketConnectionList)->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			//listen connect store
			
			//pthread_t ClientSocketStore;
			//struct Client_thread_data td;
			//td.SocketConnectionList=Client_data->SocketConnectionList;
			//td.socket_id=s1;
			//td.sender=string(inet_ntoa(s1->address.sin_addr));
			//td.receiver=string(getIPAddress());
			//rc = pthread_create(&ClientSocketStore, NULL, ClientConnect, (void *)&td );
			//if(rc)
			//{
			//	cout<<"problem in thread receiver - store"<<endl;
				
			//}
			
			cout<<"count thread"<<endl;
			pthread_t SendConnectThread; 
			struct Client_send_data t;
			t.client_lock=Client_data->client_lock;
			
			t.SocketConnectionList=Client_data->SocketConnectionList;
			t.client_num=client_num;
			t.over_ride=0;
			
			rc = pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
			if (rc)
			{
				cout<<"problem in creating thread"<<endl;
			}					
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
			
		}
		
	}	
	
}
class create_shared_space
{
//create a shared region and store list pointers

	const int SIZE = 4096; 
  
    /* name of the shared memory object */
    const char* name = "OS";
   /* shared memory file descriptor */
    int shm_fd; 
  
    /* pointer to shared memory obect */
    void* ptr; 
  
    /* create the shared memory object */
    shm_fd = shm_open(name, O_CREAT | O_RDRW, 0666); 
  
    /* configure the size of the shared memory object */
    ftruncate(shm_fd, SIZE); 
  
    /* memory map the shared memory object */
    ptr = mmap(0, SIZE, PROT_WRITE, MAP_SHARED, shm_fd, 0); 
  
    /* write to the shared memory object */
    sprintf(ptr, "%s", message_0); 
  
    ptr += strlen(message_0); 
    sprintf(ptr, "%s", message1); 
    ptr += strlen(message_1); 
  //////////////retrieve


}

int main()
{	
	//int err;
    	//pthread_mutexattr_t attr;
    	//err = pthread_mutexattr_init(&attr); if (err) return err;
    	//err = pthread_mutexattr_setpshared(&attr, PTHREAD_PROCESS_SHARED); if (err) return err;
	//for(int i=0;i<5;i++)    	
	//err = pthread_mutex_init(&client_lock[i], &attr); if (err) return err;	



	pthread_t ClientThread;
	
	if(CLIENT0==getIPAddress())
	{
		list <SocketWrapper*> SocketConnectionList;
		list <sem_t*> mutex;
		cout<<"1";
		for(int i=0;i<5;i++)
		{	
			sem_t* m1;
			m1 = (sem_t*)mmap(0, sizeof(sem_t), PROT_READ|PROT_WRITE, MAP_ANONYMOUS|MAP_SHARED, 0, 0 );
			if ((void*)m1 == MAP_FAILED) 
			{ perror("mmap");  exit(1); } 
		
			sem_init(m1, 1, 1);
			mutex.push_back(m1);
		}
	}
	cout<<"Addr at Main:"<<(&mutex)<<endl;
	int rc;
	//pthread_attr_t attr;
	//pthread_attr_init(&attr);
   	//pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);
	/*
	 
		
	//pthread_mutex_t client_lock;	
	struct thread_data td;
	td.client_lock=mutex;
	
	td.SocketConnectionList=&SocketConnectionList;
	rc = pthread_create(&ClientThread, NULL, makeConnection, (void *)&td);
	*/
	//int status=makeConnection(SocketConnectionList);//
	
	if (rc)
	{
		cout<<"Problem with the creating client connection thread... Please check the Client connection..."<<endl;
		exit(1);	
	}
	else 
	{
		cout<<"Client Connection thread Established......."<<endl;
		
	}
	while(1)
	{
	}
	
		
	return 0;
}





//////////////////////////////////////////////////////////////////////////
////////////////////////////////////////////////


#include <ifaddrs.h>
#include "connection.h"
#include <thread>
using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
//pthread_mutex_t client_lock;


string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	char buffer[1024] = {0}; 
	char *hello = "Hello"; 

	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}

	int connect_socket(char* IPname)
	{	
		address.sin_addr.s_addr = inet_addr(IPname);		
		int status;
		//cout<<address.sin_addr.s_addr<<endl;
		status=connect(socket_fd, (struct sockaddr *)&address, sizeof(address)); 
		if(status==-1)    		
		{ 
        		cout<<"Connection Failed "<<endl; 
        		return 0; 
    		}
		else
		
		{
			return 1;
		} 
    		
	}
	int listen_socket()
	{	
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, SO_REUSEADDR | SO_REUSEPORT,&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = htonl(INADDR_ANY); 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		cout<<"waiting to connect here"<<endl;
		if (listen(socket_fd, 10) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		return 1;
	}
	int return_accept_response()
	{
		return new_socket;
	}

			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	SocketWrapper()
	{
		sender="";
		receiver="";
	}
	
	Socket_connection* socket_id=new Socket_connection();
	
};
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
struct Client_send_data
{
	int client_num;
	list <SocketWrapper*> *SocketConnectionList;
	int over_ride;
	
	
};
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *setup_defer_msg = "skip";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	//pthread_mutex_lock(data->client_lock);
	if (data->over_ride==0)
	{
		
		cout<<"wait for before client to connect and send data #"<<data->client_num<<endl;
		
		while(1)
		{	
			list <SocketWrapper*> :: iterator i;
			//pthread_mutex_lock(&list_lock);
				
			for(i = (data->SocketConnectionList)->begin(); i != (data->SocketConnectionList)->end(); ++i) 
			{	
				if((*i)->sender==clientIPList[(data->client_num)-1] && (*i)->receiver==clientIPList[data->client_num])
				{	
					char buffer[1024] = {0};
					cout<<"Setup message received from "<< clientIPList[(data->client_num)-1]<<endl;
					valread = read(((*i)->socket_id)->return_accept_response()  , buffer, 1024); 
					//cout<<buffer<<endl<<" #### "<<endl;
	    				if(valread && (strcmp(buffer, "start") == 0))
					{
						received_pre_msg=1;						
						cout<<string(buffer)<<" #### "<<endl; 
						
					}
				}
			}
			
			//pthread_mutex_unlock(&list_lock);
			
			if(received_pre_msg)
			{
				break;
			}

			
		}
	    		
	}
	if (received_pre_msg or data->over_ride==1)
	{	
		for(int i=(data->client_num)+1;i<5;i++)
		{
		
			Socket_connection* s1=new Socket_connection();  //created for the sender clients
			SocketWrapper* w1=new SocketWrapper();
			w1->sender=getIPAddress();
			w1->receiver=clientIPList[i];
			//connecting the clients in mesh
			int stat=s1->connect_socket(clientIPList[i]);
			w1->socket_id=s1;
			if (stat==1)
			{	
				cout<<"verfyng connection"<<endl;
				while(1)
				{
					char buf[1024]={0};
					valread = read(s1->socket_fd  , buf, 1024); 
					
	    				if(valread && (strcmp(buf, "received") == 0))
					{
						//received_pre_msg=1;						
						cout<<string(buf)<<" #### "<<endl; 
						break;
					}
				}
				/*
				if(i==data->client_num+1)
				{
					send(s1->socket_fd , setup_msg , strlen(setup_msg) , 0 );
					cout<<"sent accept msg"<<endl;
				}
					
				else
				{
					send(s1->socket_fd , setup_defer_msg , strlen(setup_defer_msg) , 0 );
					cout<<"sent defer msg"<<endl;
				}
				*/
				pthread_mutex_lock(&list_lock);
				data->SocketConnectionList->push_front(w1); //need mutex here
				pthread_mutex_unlock(&list_lock);
				cout<<"Sender - connected "<< w1->sender <<" to "<< w1->receiver <<endl;
			}
			else
			{
				cout<<"error in sending the client connect.."<<endl;
			
			}
			
			/*
			list <SocketWrapper*> :: iterator it; 
			for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
			{
				cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
			}
			*/
	
		}
		/*
		for(int i=0;i<3;i++)
		{
		
			Socket_connection* s1=new Socket_connection();  //created for the sender clients
			SocketWrapper* w1=new SocketWrapper();
			w1->sender=getIPAddress();
			w1->receiver=serverIPList[i];
			//connecting the server in mesh
			int stat=s1->connect_socket(serverIPList[i]);
			w1->socket_id=s1;
			if (stat==1)
			{	
				pthread_mutex_lock(&list_lock);
				data->SocketConnectionList->push_front(w1); //need mutex here
				pthread_mutex_unlock(&list_lock);
				cout<<"Sender - server connected "<< w1->sender <<" to "<< w1->receiver <<endl;
			}
			else
			{
				cout<<"error in sending the server connect.."<<endl;
			
			}
			
	
		}
		*/	
		
		list <SocketWrapper*> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
		
			if((*it)->sender==clientIPList[data->client_num] && (*it)->receiver==clientIPList[(data->client_num)+1])
			{
					
				send(((*it)->socket_id)->socket_fd , setup_msg , strlen(setup_msg) , 0 ); 
				cout<<"Setup message sent to "<< clientIPList[(data->client_num)+1]<<endl;
				
			}
			
		
		}
		
		
	}
	cout<<"list ended.."<<endl;
	
}

void *makeConnection(void *threadarg)//initiate connection to all client
{	
	cout<<"Main Client Thread created"<<endl;
	struct thread_data *Client_data;
	Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return NULL;
					
		}
		
	}
	
	while(1)
	{	
		
		
		Socket_connection* s1=new Socket_connection();	
		int stat;
		if (!(connection_start==1))
			stat=s1->listen_socket();
		//pid_t pid=fork();
		//if(pid==0){
		//thread th { [=](){
	
		if (stat==1 or connection_start) 
		{	
			
			if(!(connection_start==1))
			{
				
				send(s1->return_accept_response(), setup_msg , strlen(setup_msg) , 0 ); 

				SocketWrapper* w1=new SocketWrapper();			//need mutex
				w1->sender=inet_ntoa(s1->address.sin_addr);
				w1->receiver=getIPAddress();
				w1->socket_id=s1;
				pthread_mutex_lock(&list_lock);
				(Client_data->SocketConnectionList)->push_back(w1);
				pthread_mutex_unlock(&list_lock);
				cout<<"Receiver - connected from "<<inet_ntoa(s1->address.sin_addr) <<" to "<<getIPAddress()<<endl;
			}
			if(client_num==0 or (string(clientIPList[client_num-1])==string(inet_ntoa(s1->address.sin_addr))))
			{
				pthread_t SendConnectThread; 
				struct Client_send_data t;				
				t.SocketConnectionList=Client_data->SocketConnectionList;
				t.client_num=client_num;
				//cout<<inet_ntoa(s1->address.sin_addr)<<clientIPList[client_num-1]<<endl;
				if(client_num==0)
				{
					t.over_ride=1;
				}
				else if (string(clientIPList[client_num-1])==string(inet_ntoa(s1->address.sin_addr)))
				{			
					t.over_ride=0;
				}
			
		
				pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
				if (rc)
				{
					cout<<"problem in creating thread"<<endl;
				}
			}
					
								
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		//}//--fork
		//}};  //thread th
		//th.join();
		connection_start=0;	
		
	}	
	
}

int main()
{	
	pthread_t ClientThread;
	list <SocketWrapper*> SocketConnectionList;
	int rc;
	struct thread_data td;	
	td.SocketConnectionList=&SocketConnectionList;
	rc = pthread_create(&ClientThread, NULL, makeConnection, (void *)&td);
	if (rc)
	{
		cout<<"Problem with the creating client connection thread... Please check the Client connection..."<<endl;
		exit(1);	
	}
	else 
	{
		cout<<"Client Connection thread Established......."<<endl;
		
	}
	while(1)
	{
	}
	
		
	return 0;
}



////////////---working


#include "connection.h"

using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
//pthread_mutex_t client_lock;


string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	const int opt = 1; 
	int addrlen = sizeof(address); 
	char buffer[1024] = {0}; 
	char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}

	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}

			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	SocketWrapper()
	{
		sender="";
		receiver="";
	}
	
	Socket_connection* socket_id=new Socket_connection();
	
};
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
struct Client_send_data
{
	int client_num;
	list <SocketWrapper*> *SocketConnectionList;
	int over_ride;
	
	
};
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *setup_defer_msg = "skip";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	//pthread_mutex_lock(data->client_lock);
	/*	
	if (data->over_ride==0)
	{
		
		cout<<"wait for before client to connect and send data #"<<data->client_num<<endl;
		
		while(1)
		{	
			list <SocketWrapper*> :: iterator i;
			//pthread_mutex_lock(&list_lock);
				
			for(i = (data->SocketConnectionList)->begin(); i != (data->SocketConnectionList)->end(); ++i) 
			{	
				if((*i)->sender==clientIPList[(data->client_num)-1] && (*i)->receiver==clientIPList[data->client_num])
				{	
					char buffer[1024] = {0};
					cout<<"Setup message received from "<< clientIPList[(data->client_num)-1]<<endl;
					valread = read(((*i)->socket_id)->return_accept_response()  , buffer, 1024); 
					//cout<<buffer<<endl<<" #### "<<endl;
	    				if(valread && (strcmp(buffer, "start") == 0))
					{
						received_pre_msg=1;						
						cout<<string(buffer)<<" #### "<<endl; 
						
					}
				}
			}
			
			//pthread_mutex_unlock(&list_lock);
			
			if(received_pre_msg)
			{
				break;
			}

			
		}
	    		
	}
	*/
		
	for(int i=4;i>(data->client_num);i--)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection* s1=new Socket_connection();  //created for the sender clients
		//SocketWrapper w1;//=new SocketWrapper();
		//w1->sender=getIPAddress();
		//w1->receiver=clientIPList[i];
		//connecting the clients in mesh
		int stat=s1->connect_socket(clientIPList[i]);
		//w1->socket_id=s1;
		if (stat==1)
		{	
			cout<<"verfyng connection from "<<clientIPList[i]<<endl;
			//while(1)
			//{
			
				char buf[1024]={0};
				valread = read(s1->socket_fd  , buf, 1024); 
				
    				if(valread && (strcmp(buf, "received") == 0))
				{
					//received_pre_msg=1;						
					cout<<string(buf)<<" #### "<<endl; 
					//break;
				}
			
			//}
			/*
			if(i==data->client_num+1)
			{
				send(s1->socket_fd , setup_msg , strlen(setup_msg) , 0 );
				cout<<"sent accept msg"<<endl;
			}
				
			else
			{
				send(s1->socket_fd , setup_defer_msg , strlen(setup_defer_msg) , 0 );
				cout<<"sent defer msg"<<endl;
			}
			*/
			//pthread_mutex_lock(&list_lock);
			//data->SocketConnectionList->push_front(w1); //need mutex here
			//pthread_mutex_unlock(&list_lock);
			//cout<<"Sender - connected "<< w1->sender <<" to "<< w1->receiver <<endl;
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		
		/*
		list <SocketWrapper*> :: iterator it; 
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
		}
		*/

	}
	/*
	for(int i=0;i<3;i++)
	{
	
		Socket_connection* s1=new Socket_connection();  //created for the sender clients
		SocketWrapper* w1=new SocketWrapper();
		w1->sender=getIPAddress();
		w1->receiver=serverIPList[i];
		//connecting the server in mesh
		int stat=s1->connect_socket(serverIPList[i]);
		w1->socket_id=s1;
		if (stat==1)
		{	
			pthread_mutex_lock(&list_lock);
			data->SocketConnectionList->push_front(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - server connected "<< w1->sender <<" to "<< w1->receiver <<endl;
		}
		else
		{
			cout<<"error in sending the server connect.."<<endl;
		
		}
		

	}
	*/	
		/*
		list <SocketWrapper*> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
		
			if((*it)->sender==clientIPList[data->client_num] && (*it)->receiver==clientIPList[(data->client_num)+1])
			{
					
				send(((*it)->socket_id)->socket_fd , setup_msg , strlen(setup_msg) , 0 ); 
				cout<<"Setup message sent to "<< clientIPList[(data->client_num)+1]<<endl;
				
			}
			
		
		}
		*/
		
	//}
	cout<<"list ended.."<<endl;
	
}

void *makeConnection(void *threadarg)//initiate connection to all client
{	
	cout<<"Main Client Thread created"<<endl;
	struct thread_data *Client_data;
	Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return NULL;
					
		}
		
	}
	Socket_connection* s1=new Socket_connection();
	s1->listen_socket();
	while(!(connection_start==1))
	{	
		
		
		//Socket_connection* s1=new Socket_connection();	
		
		int stat=s1->return_accept_response();
		//stat=s1->listen_socket();
		//pid_t pid=fork();
		//if(pid==0){
		//thread th { [=](){
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1->return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper* w1=new SocketWrapper();			//need mutex
			w1->sender=inet_ntoa(s1->address.sin_addr);
			w1->receiver=getIPAddress();
			w1->socket_id=s1;
			pthread_mutex_lock(&list_lock);
			(Client_data->SocketConnectionList)->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1->address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1->address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		// }
		
	}
	//sleep(1);
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=Client_data->SocketConnectionList;
	t.client_num=client_num;
	t.over_ride=0;
	//cout<<inet_ntoa(s1->address.sin_addr)<<clientIPList[client_num-1]<<endl;
	/*
	if(client_num==0)
	{
		t.over_ride=1;
	}
	else if (string(clientIPList[client_num-1])==string(inet_ntoa(s1->address.sin_addr)))
	{			
		t.over_ride=0;
	}
	*/

	pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	while(1)
	{
		cout<<"done"<<endl;
		sleep(1);	
	}
	cout<<"Connection completed"<<endl;	
	
}

int main()
{	
	pthread_t ClientThread;
	list <SocketWrapper*> SocketConnectionList;
	int rc;
	struct thread_data td;	
	td.SocketConnectionList=&SocketConnectionList;
	rc = pthread_create(&ClientThread, NULL, makeConnection, (void *)&td);
	if (rc)
	{
		cout<<"Problem with the creating client connection thread... Please check the Client connection..."<<endl;
		exit(1);	
	}
	else 
	{
		cout<<"Client Connection thread Established......."<<endl;
		
	}
	while(1)
	{
	}
	
		
	return 0;
}






/////////////////////////////////////////
////////////////



#include "connection.h"

using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t server_list_lock;
//pthread_mutex_t client_lock;
int connect_complete=0; //global variable

string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
struct Client_send_data
{
	int client_num;
	list <SocketWrapper> *SocketConnectionList;
	list <SocketWrapper> *SocketConnectionListServer;
	int over_ride;

};
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *end_msg = "end_connect";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	
	for(int i=0;i<3;i++)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=serverIPList[i];
		//connecting the clients in mesh
		int stat=s1.connect_socket(serverIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<serverIPList[i]<<endl;
				
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&server_list_lock);
			(data->SocketConnectionListServer)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&server_list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}	
	for(int i=4;i>(data->client_num);i--)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=clientIPList[i];
		//connecting the clients in mesh
		int stat=s1.connect_socket(clientIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<clientIPList[i]<<endl;
			
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&list_lock);
			(data->SocketConnectionList)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}
		
	cout<<"list ended.."<<endl;
	if(1)
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).sender<<" to "<<(*it).receiver<<endl;
	
		}
	}
	
	if(data->client_num==4)
	{
		list <SocketWrapper> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//if((*it)->sender==string(clientIPList[0]))								
			if(send(((*it).socket_id).return_accept_socket() , end_msg , strlen(end_msg) , 0 )<0)
			{
				cout<<"error in sending msg.."<<endl;	
			}
			cout<<"End message sent to "<< (*it).sender<<endl;
			sleep(1);
								
		}		
		connect_complete=1;


	}
	else
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).receiver<<" to  "<<(*it).sender<<endl;
			if((*it).receiver==string(clientIPList[4]))
			{
				
				cout<<"here"<<endl;
				char buf[1024]={0};
				valread = read(((*it).socket_id).socket_fd, buf, 1024); 
				cout<<"wait for end msg from "<<(*it).receiver<<" to me "<<(*it).sender<<endl;
				if(valread && (strcmp(buf, "end_connect") == 0))
				{
					//received_pre_msg=1;						
					//cout<<string(buf)<<" #### "<<endl;
					connect_complete=1; 
					break;
				}
					
			}
		}
	}
		
	
}

int makeConnection(list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return 0;
					
		}
		
	}
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1.address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
	
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=SocketConnectionList;
	t.SocketConnectionListServer=SocketConnectionListServer;
	t.client_num=client_num;
	t.over_ride=0;
	rc=pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	
	while(!connect_complete);
	cout<<"Connection completed"<<endl;	
	
}



/*
void *makeConnection(void *threadarg)//initiate connection to all client
{	
	cout<<"Main Client Thread created"<<endl;
	struct thread_data *Client_data;
	Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return NULL;
					
		}
		
	}
	Socket_connection* s1=new Socket_connection();
	s1->listen_socket();
	while(!(connection_start==1))
	{	
		
		
		//Socket_connection* s1=new Socket_connection();	
		
		int stat=s1->return_accept_response();
		//stat=s1->listen_socket();
		//pid_t pid=fork();
		//if(pid==0){
		//thread th { [=](){
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1->return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper* w1=new SocketWrapper();			//need mutex
			w1->sender=inet_ntoa(s1->address.sin_addr);
			w1->receiver=getIPAddress();
			w1->socket_id=s1;
			pthread_mutex_lock(&list_lock);
			(Client_data->SocketConnectionList)->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1->address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1->address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		// }
		
	}
	//sleep(1);
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=Client_data->SocketConnectionList;
	t.client_num=client_num;
	t.over_ride=0;
	//cout<<inet_ntoa(s1->address.sin_addr)<<clientIPList[client_num-1]<<endl;
	/////
	if(client_num==0)
	{
		t.over_ride=1;
	}
	else if (string(clientIPList[client_num-1])==string(inet_ntoa(s1->address.sin_addr)))
	{			
		t.over_ride=0;
	}
	/////

	pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	while(1)
	{
		cout<<"done"<<endl;
		sleep(1);	
	}
	cout<<"Connection completed"<<endl;	
	
}
*/





int main()
{	
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	N=5;//number of client nodes 
	//pthread_t ClientThread;
	list <SocketWrapper> SocketConnectionList;
	list <SocketWrapper> SocketConnectionListServer;
	int client_num;
	//int rc;
	//struct thread_data td;	
	//td.SocketConnectionList=&SocketConnectionList;
	//rc = pthread_create(&ClientThread, NULL, makeConnection, (void *)&td);
	int status=makeConnection(&SocketConnectionList,&SocketConnectionListServer);
	/*
	if (rc)
	{
		cout<<"Problem with the creating client connection thread... Please check the Client connection..."<<endl;
		exit(1);	
	}
	else 
	{
		cout<<"Client Connection thread Established......."<<endl;
		
	}	
	*/
	//message from 5 to 1 client to show the setup complete
	cout<<"*****************************************"<<endl;
	for(int i=0;i<N;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}

	


	while(1)
	{
	}
	
		
	return 0;
}





////////////////
///////- working

#include "connection.h"

using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t server_list_lock;
//pthread_mutex_t client_lock;
int connect_complete=0; //global variable

string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
struct Client_send_data
{
	int client_num;
	list <SocketWrapper> *SocketConnectionList;
	list <SocketWrapper> *SocketConnectionListServer;
	int over_ride;

};
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *end_msg = "end_connect";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	
	for(int i=0;i<3;i++)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=serverIPList[i];
		//connecting the clients in mesh
		int stat=s1.connect_socket(serverIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<serverIPList[i]<<endl;
				
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&server_list_lock);
			(data->SocketConnectionListServer)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&server_list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}	
	for(int i=4;i>(data->client_num);i--)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=clientIPList[i];
		//connecting the clients in mesh
		int stat=s1.connect_socket(clientIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<clientIPList[i]<<endl;
			
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&list_lock);
			(data->SocketConnectionList)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}
		
	cout<<"list ended.."<<endl;
	if(1)
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).sender<<" to "<<(*it).receiver<<endl;
	
		}
	}
	
	if(data->client_num==4)
	{
		list <SocketWrapper> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//if((*it)->sender==string(clientIPList[0]))								
			if(send(((*it).socket_id).return_accept_socket() , end_msg , strlen(end_msg) , 0 )<0)
			{
				cout<<"error in sending msg.."<<endl;	
			}
			cout<<"End message sent to "<< (*it).sender<<endl;
			sleep(1);
								
		}		
		connect_complete=1;


	}
	else
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).receiver<<" to  "<<(*it).sender<<endl;
			if((*it).receiver==string(clientIPList[4]))
			{
				
				cout<<"here"<<endl;
				char buf[1024]={0};
				valread = read(((*it).socket_id).socket_fd, buf, 1024); 
				cout<<"wait for end msg from "<<(*it).receiver<<" to me "<<(*it).sender<<endl;
				if(valread && (strcmp(buf, "end_connect") == 0))
				{
					//received_pre_msg=1;						
					//cout<<string(buf)<<" #### "<<endl;
					connect_complete=1; 
					break;
				}
					
			}
		}
	}
		
	
}

int makeConnection(list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return 0;
					
		}
		
	}
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1.address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
	
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=SocketConnectionList;
	t.SocketConnectionListServer=SocketConnectionListServer;
	t.client_num=client_num;
	t.over_ride=0;
	rc=pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	
	while(!connect_complete);
	cout<<"Connection completed"<<endl;	
	
}



/*
void *makeConnection(void *threadarg)//initiate connection to all client
{	
	cout<<"Main Client Thread created"<<endl;
	struct thread_data *Client_data;
	Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return NULL;
					
		}
		
	}
	Socket_connection* s1=new Socket_connection();
	s1->listen_socket();
	while(!(connection_start==1))
	{	
		
		
		//Socket_connection* s1=new Socket_connection();	
		
		int stat=s1->return_accept_response();
		//stat=s1->listen_socket();
		//pid_t pid=fork();
		//if(pid==0){
		//thread th { [=](){
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1->return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper* w1=new SocketWrapper();			//need mutex
			w1->sender=inet_ntoa(s1->address.sin_addr);
			w1->receiver=getIPAddress();
			w1->socket_id=s1;
			pthread_mutex_lock(&list_lock);
			(Client_data->SocketConnectionList)->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1->address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1->address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		// }
		
	}
	//sleep(1);
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=Client_data->SocketConnectionList;
	t.client_num=client_num;
	t.over_ride=0;
	//cout<<inet_ntoa(s1->address.sin_addr)<<clientIPList[client_num-1]<<endl;
	/////
	if(client_num==0)
	{
		t.over_ride=1;
	}
	else if (string(clientIPList[client_num-1])==string(inet_ntoa(s1->address.sin_addr)))
	{			
		t.over_ride=0;
	}
	/////

	pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	while(1)
	{
		cout<<"done"<<endl;
		sleep(1);	
	}
	cout<<"Connection completed"<<endl;	
	
}
*/
int main()
{	
	pthread_t ClientThread;
	list <SocketWrapper> SocketConnectionList;
	list <SocketWrapper> SocketConnectionListServer;
	//int rc;
	//struct thread_data td;	
	//td.SocketConnectionList=&SocketConnectionList;
	//rc = pthread_create(&ClientThread, NULL, makeConnection, (void *)&td);
	int status=makeConnection(&SocketConnectionList,&SocketConnectionListServer);
	/*
	if (rc)
	{
		cout<<"Problem with the creating client connection thread... Please check the Client connection..."<<endl;
		exit(1);	
	}
	else 
	{
		cout<<"Client Connection thread Established......."<<endl;
		
	}	
	*/
	//message from 5 to 1 client to show the setup complete
	while(1)
	{
	}
	
		
	return 0;
}



/////////// ricart
///////// stating





#include "connection.h"

using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t server_list_lock;
pthread_mutex_t Seq_num_lock;
pthread_mutex_t Reply_expected_lock;
//pthread_mutex_t client_lock;
int connect_complete=0; //global variable

string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
struct Client_send_data
{
	int client_num;
	list <SocketWrapper> *SocketConnectionList;
	list <SocketWrapper> *SocketConnectionListServer;
	int over_ride;

};
int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *end_msg = "end_connect";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	
	for(int i=0;i<3;i++)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=serverIPList[i];
		w1.connect_num=findClientNum(string(serverIPList[i]));
		//connecting the clients in mesh
		int stat=s1.connect_socket(serverIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<serverIPList[i]<<endl;
				
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&server_list_lock);
			(data->SocketConnectionListServer)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&server_list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}	
	for(int i=4;i>(data->client_num);i--)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=clientIPList[i];
		w1.connect_num=findClientNum(string(clientIPList[i]));
		//connecting the clients in mesh
		int stat=s1.connect_socket(clientIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<clientIPList[i]<<endl;
			
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&list_lock);
			(data->SocketConnectionList)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}
	/*	
	cout<<"list ended.."<<endl;
	if(1)
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).sender<<" to "<<(*it).receiver<<endl;
	
		}
	}
	*/
	if(data->client_num==4)
	{
		list <SocketWrapper> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//if((*it)->sender==string(clientIPList[0]))								
			if(send(((*it).socket_id).return_accept_socket() , end_msg , strlen(end_msg) , 0 )<0)
			{
				cout<<"error in sending msg.."<<endl;	
			}
			cout<<"End message sent to "<< (*it).sender<<endl;
			//sleep(1);
								
		}		
		connect_complete=1;


	}
	else
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).receiver<<" to  "<<(*it).sender<<endl;
			if((*it).receiver==string(clientIPList[4]))
			{
				
				cout<<"here"<<endl;
				char buf[1024]={0};
				valread = read(((*it).socket_id).socket_fd, buf, 1024); 
				cout<<"wait for end msg from "<<(*it).receiver<<" to me "<<(*it).sender<<endl;
				if(valread && (strcmp(buf, "end_connect") == 0))
				{
					//received_pre_msg=1;						
					//cout<<string(buf)<<" #### "<<endl;
					connect_complete=1; 
					break;
				}
					
			}
		}
	}
		
	
}

int makeConnection(list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return 0;
					
		}
		
	}
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1.address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
	
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=SocketConnectionList;
	t.SocketConnectionListServer=SocketConnectionListServer;
	t.client_num=client_num;
	t.over_ride=0;
	rc=pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	
	while(!connect_complete);
	cout<<"Connection completed"<<endl;	
	
}

struct Request_thread_data
{
	int client_num;
	//list <SocketWrapper> *SocketConnectionList;
	//list <SocketWrapper> *SocketConnectionListServer;
	int *Seq_num;
	int socket;
	int *Reply_expected;

};
struct Reply_thread_data
{
	int client_num;
	int *Seq_num;
	int socket;
	int *Reply_expected;
	bool *Reply_Deferred;
	int *Highest_Seq_num;
	bool *Critical_Section;
};
void *REQUEST_CS(void *threadarg)
{
	struct Request_thread_data *data;
	data = (struct Request_thread_data *) threadarg;
	cout<<data->client_num<<" "<<data->Seq_num<<" "<<endl;
	char req_msg[100]={0};
	
	snprintf( req_msg, sizeof(req_msg), "%s_%d:%d", "REQ", data->client_num,data->Seq_num);
	cout<<req_msg<<"... msg sent"<<endl;
	send(data->socket, &req_msg , strlen(req_msg) , 0 );
	char buf[100]={0};
	int valread = read(data->socket, buf, 100); 
	if(valread && (strcmp(buf, "REPLY") == 0))
	{
		data->Reply_expected--;
	}
}
void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data *data;
	data = (struct Reply_thread_data *) threadarg;
	cout<<data->client_num<<" "<<data->Seq_num<<" "<<endl;
	int Client_Req_num;// j
	int Req_Seq_num;//k
	char* reply_msg;
	bool Defer_it=FALSE;
	//int Highest_Seq_num=0;
	while(1)
	{
		char buf[100]={0};
		int valread = read(data->socket, buf, 100); 
		
		reply_msg = strtok(buf, ":");
		cout<<buf<<" 888 "<<reply_msg<<endl;
		if (string(buf)=="exit")
			break;
		else if (reply_msg=="REQ")
		{
			reply_msg = strtok(NULL, ":"); 
    			Client_Req_num=atoi(reply_msg); 
    			reply_msg = strtok(NULL, ":");
			Req_Seq_num=atoi(reply_msg);
			cout<<Client_Req_num<<" &&& "<<Req_Seq_num<<endl;

		}
		Highest_Seq_num=max(data->Highest_Seq_num,Req_Seq_num);
		if(data->Critical_Section && ((Req_Seq_num>data->Seq_num) || ((Req_Seq_num == data->Seq_num) && (Client_Req_num > data->client_num)))
		{
			Defer_it=TRUE;	
		}
		if (Defer_it==TRUE)
		{
			data->Reply_Deferred[Client_Req_num]=TRUE;
		}
		else
		{
			char reply_msg[100]={0};	
			snprintf( reply_msg, sizeof(reply_msg), "%s:%d", "REPLY", data->client_num);
			cout<<reply_msg<<"... msg sent"<<endl;
			send(data->socket, &reply_msg , strlen(reply_msg) , 0 );
		}
	}

}
int ricart_agrawala_algo(int N,int client_num,list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{
	int Seq_num=0;
	int Highest_Seq_num=0;
	int Reply_expected=N-1;
	bool Critical_Section=FALSE;
	bool Reply_Deferred[5]={0};
	char reply[100]={0};
		
	
	pthread_t REPLY[5];
	struct Reply_thread_data Rep[5];		
	Rep.client_num=client_num;
	Rep.Seq_num=&Seq_num;
	Rep.Reply_expected=&Reply_expected;
	Rep.Reply_Deferred=Reply_Deferred;
	int rc = pthread_create(&REQ[s], NULL, REQUEST_CS, (void *)&Req[s]);


	pthread_t REQ[5];
	struct Request_thread_data Req[5];
	int s;
	list <SocketWrapper> :: iterator it;


	if (rc)
	{
		cout<<"Problem with the creating Reply Thread.."<<endl;
		return 0;	
	}
	if(1)
	{
		usleep(30000);
		cout<<"Request entry to the Critical Section"<<endl;
		Reply_expected=N-1;
		Critical_Section=TRUE;
		Seq_num=Highest_Seq_num+1;
		
		for(s=0, it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it,s++) 
		{
			
			Req[s].client_num=client_num;
			Req[s].Seq_num=&Seq_num;
			Req[s].Reply_expected=&Reply_expected;
			if((*it).connect_num>client_num)
			{
				Req[s].socket=((*it).socket_id).socket_fd;
			}
			else
			{
				Req[s].socket=((*it).socket_id).return_accept_socket();
			}
			
			int rc = pthread_create(&REQ[s], NULL, REQUEST_CS, (void *)&Req[s]);
			if (rc)
			{
				cout<<"Problem with the creating Request Thread.."<<endl;
				return 0;	
			}

		}
		cout<<"wait to get reply from others"<<endl;
		while(!Reply_expected==0);
		cout<<"PERFORM CRITICAL SECTION"<<endl;
		Critical_Section=FALSE;
		for(int i=0;i<N;i++)
		{
			if(Reply_Deferred[i]==TRUE)
			{
				Reply_Deferred[i]=FALSE;
				for(it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it) 
				{
					if((*it).connect_num==i)
					{
						snprintf( reply, sizeof(reply), "%s:%d", "REPLY", client_num);
						if(i>client_num)
							send(((*it).socket_id).socket_fd,&reply , strlen(reply) , 0);
						else
							send(((*it).socket_id).return_accept_socket(),&reply , strlen(reply) , 0);
					}
				}
			}
		}
	}

}

int main()
{	
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num;
	client_num=findClientNum(getIPAddress());
	cout<<client_num;
	//pthread_t ClientThread;
	list <SocketWrapper> SocketConnectionList;
	list <SocketWrapper> SocketConnectionListServer;
	int status=makeConnection(&SocketConnectionList,&SocketConnectionListServer);
	cout<<"*****************Connection Established******************"<<endl;
	int stat=ricart_agrawala_algo(N,client_num,&SocketConnectionList,&SocketConnectionListServer);
	while(1)
	{
	}
	
		
	return 0;
}




///////////////////ricart-agrawala working

#include "connection.h"

using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t server_list_lock;
pthread_mutex_t Seq_num_lock;
pthread_mutex_t Reply_expected_lock;
pthread_mutex_t Highest_Seq_num_lock;
	
//pthread_mutex_t client_lock;
int connect_complete=0; //global variable

string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
struct Client_send_data
{
	int client_num;
	list <SocketWrapper> *SocketConnectionList;
	list <SocketWrapper> *SocketConnectionListServer;
	int over_ride;

};
int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *end_msg = "end_connect";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	
	for(int i=0;i<3;i++)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=serverIPList[i];
		w1.connect_num=findClientNum(string(serverIPList[i]));
		//connecting the clients in mesh
		int stat=s1.connect_socket(serverIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<serverIPList[i]<<endl;
				
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&server_list_lock);
			(data->SocketConnectionListServer)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&server_list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}	
	for(int i=4;i>(data->client_num);i--)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=clientIPList[i];
		w1.connect_num=findClientNum(string(clientIPList[i]));
		//connecting the clients in mesh
		int stat=s1.connect_socket(clientIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<clientIPList[i]<<endl;
			
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&list_lock);
			(data->SocketConnectionList)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}
	/*	
	cout<<"list ended.."<<endl;
	if(1)
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).sender<<" to "<<(*it).receiver<<endl;
	
		}
	}
	*/
	if(data->client_num==4)
	{
		list <SocketWrapper> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//if((*it)->sender==string(clientIPList[0]))								
			if(send(((*it).socket_id).return_accept_socket() , end_msg , strlen(end_msg) , 0 )<0)
			{
				cout<<"error in sending msg.."<<endl;	
			}
			cout<<"End message sent to "<< (*it).sender<<endl;
			//sleep(1);
								
		}		
		connect_complete=1;


	}
	else
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).receiver<<" to  "<<(*it).sender<<endl;
			if((*it).receiver==string(clientIPList[4]))
			{
				
				cout<<"here"<<endl;
				char buf[1024]={0};
				valread = read(((*it).socket_id).socket_fd, buf, 1024); 
				cout<<"wait for end msg from "<<(*it).receiver<<" to me "<<(*it).sender<<endl;
				if(valread && (strcmp(buf, "end_connect") == 0))
				{
					//received_pre_msg=1;						
					//cout<<string(buf)<<" #### "<<endl;
					connect_complete=1; 
					break;
				}
					
			}
		}
	}
		
	
}

int makeConnection(list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return 0;
					
		}
		
	}
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1.address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
	
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=SocketConnectionList;
	t.SocketConnectionListServer=SocketConnectionListServer;
	t.client_num=client_num;
	t.over_ride=0;
	rc=pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	
	while(!connect_complete);
	cout<<"Connection completed"<<endl;	
	
}

struct Request_thread_data
{
	int client_num;
	//list <SocketWrapper> *SocketConnectionList;
	//list <SocketWrapper> *SocketConnectionListServer;
	int *Seq_num;
	int socket;
	int *Reply_expected;

};
struct Reply_thread_data
{
	int client_num;
	int *Seq_num;
	int socket;
	int *Reply_expected;
	bool *Reply_Deferred;
	int *Highest_Seq_num;
	bool *Critical_Section;
};
void *REQUEST_CS(void *threadarg)
{
	struct Request_thread_data *data;
	data = (struct Request_thread_data *) threadarg;
	//cout<<data->client_num<<" "<<*data->Seq_num<<" "<<endl;
	char req_msg[100]={0};
	
	snprintf( req_msg, sizeof(req_msg), "%s:%d:%d", "REQ", data->client_num,*data->Seq_num);
	//cout<<req_msg<<"... msg sent"<<endl;
	send(data->socket, &req_msg , strlen(req_msg) , 0 );
	//char buf[100]={0};
	//int valread = read(data->socket, buf, 100); 
	//if(valread && (strcmp(buf, "REPLY") == 0))
	//{
	//	data->Reply_expected--;
	//	cout<<data->Reply_expected;
	//}
	
}
void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data *data;
	data = (struct Reply_thread_data *) threadarg;
	//cout<<data->client_num<<" "<<data->Seq_num<<" "<<endl;
	int Client_Req_num;// j
	int Req_Seq_num;//k
	char* reply_msg;
	char* temp;
	bool Defer_it=FALSE;
	//int Highest_Seq_num=0;
	while(1)
	{	
		cout<<"waiting for any req msg"<<endl;
		char buf[100]={0};
		
		int valread = read(data->socket, buf, 100); 
		string buffer(buf);
		//cout<<temp;
		size_t found = buffer.find("REPLY");
  		size_t found1 = buffer.find("REQ");
		if(valread && found!=string::npos)
		{
			pthread_mutex_lock(&Reply_expected_lock);
			*data->Reply_expected=*data->Reply_expected-1;
			pthread_mutex_unlock(&Reply_expected_lock);
			temp=strtok(buf,":");
			temp=strtok(NULL,":");
			cout<<"got reply - waiting reply from "<<temp<<" still waiting for "<<*data->Reply_expected<<"replies"<<endl;
			valread=0;
		}
		else if (valread && found1!=string::npos)
		{
			reply_msg = strtok(buf, ":");
			//cout<<buf<<" 888 "<<reply_msg<<endl;
			
			//else if (string(reply_msg)=="REQ")
			//{
				reply_msg = strtok(NULL, ":"); 
	    			Client_Req_num=atoi(reply_msg); 
				
	    			reply_msg = strtok(NULL, ":");
				Req_Seq_num=atoi(reply_msg);
				//cout<<Client_Req_num<<" &&& "<<Req_Seq_num<<endl;

			//}
			pthread_mutex_lock(&Highest_Seq_num_lock);
			*(data->Highest_Seq_num)=max(*(data->Highest_Seq_num),Req_Seq_num);
			pthread_mutex_unlock(&Highest_Seq_num_lock);
			//cout<<*(data->Highest_Seq_num)<<" -- "<<*data->Critical_Section<<" - "<<Req_Seq_num<<" - "<<*(data->Seq_num)<<endl;
			if((*data->Critical_Section) && ((Req_Seq_num > *data->Seq_num) || ((Req_Seq_num == *(data->Seq_num)) && (Client_Req_num > data->client_num))))
			{
				Defer_it=TRUE;	
				cout<<"Defered reply to "<<Client_Req_num<<endl;
			}
			if (Defer_it==TRUE)
			{
				data->Reply_Deferred[Client_Req_num]=TRUE;
				//cout<<"List Defered reply to "<<data->Reply_Deferred[Client_Req_num];
			}
			else
			{
				char reply_msg[100]={0};	
				snprintf( reply_msg, sizeof(reply_msg), "%s:%d", "REPLY",data->client_num);
				cout<<"Sent Reply to "<<Client_Req_num<<endl;
				send(data->socket, &reply_msg , strlen(reply_msg) , 0 );
			}
		}
		//cout<<"reply exit.."<<*data->Reply_expected<<endl;
	
	}

}
int ricart_agrawala_algo(int N,int client_num,list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{
	int Seq_num=0;
	int Highest_Seq_num=0;
	int Reply_expected=N-1;
	bool Critical_Section=FALSE;
	bool Reply_Deferred[5]={0};
	char reply[100]={0};
		
	
	pthread_t REPLY[5];
	struct Reply_thread_data Rep[5];
	int l;
	list <SocketWrapper> :: iterator itt;
	for(l=0, itt = SocketConnectionList->begin(); itt != SocketConnectionList->end(); ++itt,l++) 
	{		
		Rep[l].client_num=client_num;
		Rep[l].Seq_num=&Seq_num;
		Rep[l].Reply_expected=&Reply_expected;
		Rep[l].Reply_Deferred=Reply_Deferred;
		Rep[l].Highest_Seq_num=&Highest_Seq_num;
		Rep[l].Critical_Section=&Critical_Section;
		if((*itt).connect_num>client_num)
		{
			Rep[l].socket=((*itt).socket_id).socket_fd;
		}
		else
		{
			Rep[l].socket=((*itt).socket_id).return_accept_socket();
		}
	
	
		int rc = pthread_create(&REPLY[l], NULL, REPLY_CS, (void *)&Rep[l]);
		if (rc)
		{
			cout<<"Problem with the creating Reply Thread.."<<endl;
			return 0;	
		}
	}
	pthread_t REQ[5];
	struct Request_thread_data Req[5];
	int s;
	list <SocketWrapper> :: iterator it;


	for(int count=0;count<1;count++)
	//if(getIPAddress()==string(CLIENT0))
	{
		usleep(5000000);
		cout<<"Request entry to the Critical Section"<<endl;
		Reply_expected=N-1;
		Critical_Section=TRUE;
		Seq_num=Highest_Seq_num+1;
		
		for(s=0, it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it,s++) 
		{
			
			Req[s].client_num=client_num;
			Req[s].Seq_num=&Seq_num;
			Req[s].Reply_expected=&Reply_expected;
			if((*it).connect_num>client_num)
			{
				Req[s].socket=((*it).socket_id).socket_fd;
			}
			else
			{
				Req[s].socket=((*it).socket_id).return_accept_socket();
			}
			
			int rc = pthread_create(&REQ[s], NULL, REQUEST_CS, (void *)&Req[s]);
			if (rc)
			{
				cout<<"Problem with the creating Request Thread.."<<endl;
				return 0;	
			}

		}
		
		while(!Reply_expected==0);
		cout<<"PERFORM CRITICAL SECTION"<<endl;
		Critical_Section=FALSE;
		for(int i=0;i<N;i++)
		{
			if(Reply_Deferred[i]==TRUE)
			{
				Reply_Deferred[i]=FALSE;
				for(it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it) 
				{
					if((*it).connect_num==i)
					{
						snprintf( reply, sizeof(reply), "%s:%d", "REPLY",client_num);
						if(i>client_num)
							send(((*it).socket_id).socket_fd,&reply , strlen(reply) , 0);
						else
							send(((*it).socket_id).return_accept_socket(),&reply , strlen(reply) , 0);
						cout<<"Sent Deferred Reply to "<<i<<endl;
					}
				}
			}
		}
	}

}

int main()
{	
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num;
	client_num=findClientNum(getIPAddress());
	cout<<client_num;
	//pthread_t ClientThread;
	list <SocketWrapper> SocketConnectionList;
	list <SocketWrapper> SocketConnectionListServer;
	int status=makeConnection(&SocketConnectionList,&SocketConnectionListServer);
	cout<<"*****************Connection Established******************"<<endl;
	int stat=ricart_agrawala_algo(N,client_num,&SocketConnectionList,&SocketConnectionListServer);
	while(1)
	{
	}
	
		
	return 0;
}




//////////////////////////////-before optimization ricart agarwala working -client

#include "connection.h"

using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t server_list_lock;
pthread_mutex_t Seq_num_lock;
pthread_mutex_t Reply_expected_lock;
pthread_mutex_t Highest_Seq_num_lock;
	
//pthread_mutex_t client_lock;
int connect_complete=0; //global variable

string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
struct Client_send_data
{
	int client_num;
	list <SocketWrapper> *SocketConnectionList;
	list <SocketWrapper> *SocketConnectionListServer;
	int over_ride;

};
int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *end_msg = "end_connect";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	
	for(int i=0;i<3;i++)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=serverIPList[i];
		w1.connect_num=i;
		//connecting the clients in mesh
		int stat=s1.connect_socket(serverIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<serverIPList[i]<<endl;
				
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&server_list_lock);
			(data->SocketConnectionListServer)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&server_list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}	
	for(int i=4;i>(data->client_num);i--)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=clientIPList[i];
		w1.connect_num=findClientNum(string(clientIPList[i]));
		//connecting the clients in mesh
		int stat=s1.connect_socket(clientIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<clientIPList[i]<<endl;
			
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&list_lock);
			(data->SocketConnectionList)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}
	/*	
	cout<<"list ended.."<<endl;
	if(1)
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).sender<<" to "<<(*it).receiver<<endl;
	
		}
	}
	*/
	if(data->client_num==4)
	{
		list <SocketWrapper> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//if((*it)->sender==string(clientIPList[0]))								
			if(send(((*it).socket_id).return_accept_socket() , end_msg , strlen(end_msg) , 0 )<0)
			{
				cout<<"error in sending msg.."<<endl;	
			}
			cout<<"End message sent to "<< (*it).sender<<endl;
			//sleep(1);
								
		}		
		connect_complete=1;


	}
	else
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).receiver<<" to  "<<(*it).sender<<endl;
			if((*it).receiver==string(clientIPList[4]))
			{
				
				cout<<"here"<<endl;
				char buf[1024]={0};
				valread = read(((*it).socket_id).socket_fd, buf, 1024); 
				cout<<"wait for end msg from "<<(*it).receiver<<" to me "<<(*it).sender<<endl;
				if(valread && (strcmp(buf, "end_connect") == 0))
				{
					//received_pre_msg=1;						
					//cout<<string(buf)<<" #### "<<endl;
					connect_complete=1; 
					break;
				}
					
			}
		}
	}
		
	
}

int makeConnection(list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return 0;
					
		}
		
	}
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1.address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
	
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=SocketConnectionList;
	t.SocketConnectionListServer=SocketConnectionListServer;
	t.client_num=client_num;
	t.over_ride=0;
	rc=pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	
	while(!connect_complete);
	cout<<"Connection completed"<<endl;	
	
}

struct Request_thread_data
{
	int client_num;
	//list <SocketWrapper> *SocketConnectionList;
	//list <SocketWrapper> *SocketConnectionListServer;
	int *Seq_num;
	int socket;
	int *Reply_expected;

};
struct Reply_thread_data
{
	int client_num;
	int *Seq_num;
	int socket;
	int *Reply_expected;
	bool *Reply_Deferred;
	int *Highest_Seq_num;
	bool *Critical_Section;
};
void *REQUEST_CS(void *threadarg)
{
	struct Request_thread_data *data;
	data = (struct Request_thread_data *) threadarg;
	//cout<<data->client_num<<" "<<*data->Seq_num<<" "<<endl;
	char req_msg[100]={0};
	
	snprintf( req_msg, sizeof(req_msg), "%s:%d:%d", "REQ", data->client_num,*data->Seq_num);
	//cout<<req_msg<<"... msg sent"<<endl;
	send(data->socket, &req_msg , strlen(req_msg) , 0 );
	//char buf[100]={0};
	//int valread = read(data->socket, buf, 100); 
	//if(valread && (strcmp(buf, "REPLY") == 0))
	//{
	//	data->Reply_expected--;
	//	cout<<data->Reply_expected;
	//}
	
}
void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data *data;
	data = (struct Reply_thread_data *) threadarg;
	//cout<<data->client_num<<" "<<data->Seq_num<<" "<<endl;
	int Client_Req_num;// j
	int Req_Seq_num;//k
	char* reply_msg;
	char* temp;
	bool Defer_it=FALSE;
	//int Highest_Seq_num=0;
	while(1)
	{	
		cout<<"waiting for any req msg"<<endl;
		char buf[100]={0};
		
		int valread = read(data->socket, buf, 100); 
		string buffer(buf);
		//cout<<temp;
		size_t found = buffer.find("REPLY");
  		size_t found1 = buffer.find("REQ");
		if(valread && found!=string::npos)
		{
			pthread_mutex_lock(&Reply_expected_lock);
			*data->Reply_expected=*data->Reply_expected-1;
			pthread_mutex_unlock(&Reply_expected_lock);
			temp=strtok(buf,":");
			temp=strtok(NULL,":");
			cout<<"got reply - waiting reply from "<<temp<<" still waiting for "<<*data->Reply_expected<<"replies"<<endl;
			valread=0;
		}
		else if (valread && found1!=string::npos)
		{
			reply_msg = strtok(buf, ":");
			//cout<<buf<<" 888 "<<reply_msg<<endl;
			
			//else if (string(reply_msg)=="REQ")
			//{
				reply_msg = strtok(NULL, ":"); 
	    			Client_Req_num=atoi(reply_msg); 
				
	    			reply_msg = strtok(NULL, ":");
				Req_Seq_num=atoi(reply_msg);
				//cout<<Client_Req_num<<" &&& "<<Req_Seq_num<<endl;

			//}
			pthread_mutex_lock(&Highest_Seq_num_lock);
			*(data->Highest_Seq_num)=max(*(data->Highest_Seq_num),Req_Seq_num);
			pthread_mutex_unlock(&Highest_Seq_num_lock);
			//cout<<*(data->Highest_Seq_num)<<" -- "<<*data->Critical_Section<<" - "<<Req_Seq_num<<" - "<<*(data->Seq_num)<<endl;
			if((*data->Critical_Section) && ((Req_Seq_num > *data->Seq_num) || ((Req_Seq_num == *(data->Seq_num)) && (Client_Req_num > data->client_num))))
			{
				Defer_it=TRUE;	
				cout<<"Defered reply to "<<Client_Req_num<<endl;
			}
			if (Defer_it==TRUE)
			{
				data->Reply_Deferred[Client_Req_num]=TRUE;
				//cout<<"List Defered reply to "<<data->Reply_Deferred[Client_Req_num];
			}
			else
			{
				char reply_msg[100]={0};	
				snprintf( reply_msg, sizeof(reply_msg), "%s:%d", "REPLY",data->client_num);
				cout<<"Sent Reply to "<<Client_Req_num<<endl;
				send(data->socket, &reply_msg , strlen(reply_msg) , 0 );
			}
		}
		//cout<<"reply exit.."<<*data->Reply_expected<<endl;
	
	}

}
int ricart_agrawala_algo(int N,int client_num,list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{
	int Seq_num=0;
	int Highest_Seq_num=0;
	int Reply_expected=N-1;
	bool Critical_Section=FALSE;
	bool Reply_Deferred[5]={0};
	char reply[100]={0};
	char* ServerReq="WRITE";	
	
	pthread_t REPLY[5];
	struct Reply_thread_data Rep[5];
	int l;
	list <SocketWrapper> :: iterator itt;
	for(l=0, itt = SocketConnectionList->begin(); itt != SocketConnectionList->end(); ++itt,l++) 
	{		
		Rep[l].client_num=client_num;
		Rep[l].Seq_num=&Seq_num;
		Rep[l].Reply_expected=&Reply_expected;
		Rep[l].Reply_Deferred=Reply_Deferred;
		Rep[l].Highest_Seq_num=&Highest_Seq_num;
		Rep[l].Critical_Section=&Critical_Section;
		if((*itt).connect_num>client_num)
		{
			Rep[l].socket=((*itt).socket_id).socket_fd;
		}
		else
		{
			Rep[l].socket=((*itt).socket_id).return_accept_socket();
		}
	
	
		int rc = pthread_create(&REPLY[l], NULL, REPLY_CS, (void *)&Rep[l]);
		if (rc)
		{
			cout<<"Problem with the creating Reply Thread.."<<endl;
			return 0;	
		}
	}
	pthread_t REQ[5];
	struct Request_thread_data Req[5];
	int s;
	list <SocketWrapper> :: iterator it;


	for(int count=0;count<1;count++)
	//if(getIPAddress()==string(CLIENT0))
	{
		usleep(5000000);
		cout<<"Request entry to the Critical Section"<<endl;
		Reply_expected=N-1;
		Critical_Section=TRUE;
		Seq_num=Highest_Seq_num+1;
		
		for(s=0, it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it,s++) 
		{
			
			Req[s].client_num=client_num;
			Req[s].Seq_num=&Seq_num;
			Req[s].Reply_expected=&Reply_expected;
			if((*it).connect_num>client_num)
			{
				Req[s].socket=((*it).socket_id).socket_fd;
			}
			else
			{
				Req[s].socket=((*it).socket_id).return_accept_socket();
			}
			
			int rc = pthread_create(&REQ[s], NULL, REQUEST_CS, (void *)&Req[s]);
			if (rc)
			{
				cout<<"Problem with the creating Request Thread.."<<endl;
				return 0;	
			}

		}
		
		while(!Reply_expected==0);
		cout<<"PERFORM CRITICAL SECTION"<<endl;
		srand (time(NULL));
		int server_no=rand() % 3;//contact a random server to write to file
		cout<<"Connecting to Random Server "<<server_no<<endl;;
		list <SocketWrapper> :: iterator it1;
		
		for(it1 = SocketConnectionListServer->begin(); it1 != SocketConnectionListServer->end(); ++it1) 
		{
			cout<<(*it1).connect_num<<endl;
			if((*it1).connect_num==server_no)
			{
				cout<<"req sent to server"<<endl;
				send(((*it1).socket_id).socket_fd,&ServerReq , strlen(ServerReq) , 0);
				char buf[100]={0};
		
				int valread = read(((*it1).socket_id).socket_fd, buf, 100); 
				string buffer(buf);

				size_t found = buffer.find("finish");
  				
				if(valread && found!=string::npos)
				{
					cout<<"done with Critical Section"<<endl;
				}
				
			}
		}
		Critical_Section=FALSE;
		for(int i=0;i<N;i++)
		{
			if(Reply_Deferred[i]==TRUE)
			{
				Reply_Deferred[i]=FALSE;
				for(it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it) 
				{
					if((*it).connect_num==i)
					{
						snprintf( reply, sizeof(reply), "%s:%d", "REPLY",client_num);
						if(i>client_num)
						{
							send(((*it).socket_id).socket_fd,&reply , strlen(reply) , 0);
						}
						else
						{
							send(((*it).socket_id).return_accept_socket(),&reply , strlen(reply) , 0);
						}
						cout<<"Sent Deferred Reply to "<<i<<endl;
					}
				}
			}
		}
	}

}

int main()
{	
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num;
	client_num=findClientNum(getIPAddress());
	cout<<client_num;
	//pthread_t ClientThread;
	list <SocketWrapper> SocketConnectionList;
	list <SocketWrapper> SocketConnectionListServer;
	int status=makeConnection(&SocketConnectionList,&SocketConnectionListServer);
	cout<<"*****************Connection Established******************"<<endl;
	int stat=ricart_agrawala_algo(N,client_num,&SocketConnectionList,&SocketConnectionListServer);
	cout<<"**DONE**"<<endl;
	while(1)
	{
	}
	
		
	return 0;
}
/////-server
#include <ifaddrs.h>
#include "connection.h"
#include <thread>
using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
//pthread_mutex_t client_lock;


string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
/*
struct Client_send_data
{
	int client_num;
	list <SocketWrapper*> *SocketConnectionList;
	int over_ride;
	
	
};
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char buffer[1024] = {0};
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	//pthread_mutex_lock(data->client_lock);
	if (data->over_ride==0)
	{
		
		cout<<"wait for before client to connect and send data #"<<data->client_num<<endl;
		
		while(1)
		{	
			list <SocketWrapper*> :: iterator i;
			//pthread_mutex_lock(&list_lock);
				
			for(i = (data->SocketConnectionList)->begin(); i != (data->SocketConnectionList)->end(); ++i) 
			{	
				if((*i)->sender==clientIPList[(data->client_num)-1] && (*i)->receiver==clientIPList[data->client_num])
				{
					cout<<"Setup message received from "<< clientIPList[(data->client_num)-1]<<endl;
					valread = read(((*i)->socket_id)->return_accept_response()  , buffer, 1024); 
					//cout<<buffer<<endl<<" #### "<<endl;
	    				if(valread && (strcmp(buffer, "start") == 0))
					{
						received_pre_msg=1;						
						cout<<string(buffer)<<endl<<" #### "<<endl; 
						
					}
				}
			}
			
			//pthread_mutex_unlock(&list_lock);
			
			if(received_pre_msg)
			{
				break;
			}

			
		}
	    		
	}
	if (received_pre_msg or data->over_ride==1)
	{	
		for(int i=(data->client_num)+1;i<5;i++)
		{
		
			Socket_connection* s1=new Socket_connection();  //created for the sender clients
			SocketWrapper* w1=new SocketWrapper();
			w1->sender=getIPAddress();
			w1->receiver=clientIPList[i];
			//connecting the clients in mesh
			int stat=s1->connect_socket(clientIPList[i]);
			w1->socket_id=s1;
			if (stat==1)
			{	
				pthread_mutex_lock(&list_lock);
				data->SocketConnectionList->push_front(w1); //need mutex here
				pthread_mutex_unlock(&list_lock);
				cout<<"Sender - connected "<< w1->sender <<" to "<< w1->receiver <<endl;
			}
			else
			{
				cout<<"error in sending the client connect.."<<endl;
			
			}
			
			
			//list <SocketWrapper*> :: iterator it; 
			//for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
			//{
			//	cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
			//}
			
	
		}
	
	
		list <SocketWrapper*> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
		
			if((*it)->sender==clientIPList[data->client_num] && (*it)->receiver==clientIPList[(data->client_num)+1])
			{
					
				send(((*it)->socket_id)->socket_fd , setup_msg , strlen(setup_msg) , 0 ); 
				cout<<"Setup message sent to "<< clientIPList[(data->client_num)+1]<<endl;
				break;
			}
		
		}
		
		
	}
	cout<<"list ended.."<<endl;
	
}
*/


int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}


int makeConnection(list <SocketWrapper> *SocketConnectionList)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (SocketConnectionList->size()==5)
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
		
	cout<<"Connection completed"<<endl;	
	
}
struct Reply_thread_data
{
	int socket;
	int client_num;
};

void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data *data;
	data = (struct Reply_thread_data *) threadarg;
	char *finish_msg = "finish";
	while(1)
	{
		cout<<"waiting for any req msg from clients"<<endl;
		char buf[100]={0};
		
		int valread = read(data->socket, buf, 100); 
		//string buffer(buf);
		cout<<"message received "<<buf<<" from "<<data->client_num<<endl;
		sleep(5);
		send(data->socket, &finish_msg , strlen(finish_msg) , 0 );
	}

}
int ReplyClient(list <SocketWrapper> *SocketConnectionList)
{
	pthread_t REPLY[5];
	struct Reply_thread_data Rep[5];
	int l;
	list <SocketWrapper> :: iterator itt;
	for(l=0, itt = SocketConnectionList->begin(); itt != SocketConnectionList->end(); ++itt,l++) 
	{
		Rep[l].socket=((*itt).socket_id).return_accept_socket();
		Rep[l].client_num=(*itt).connect_num;
		int rc = pthread_create(&REPLY[l], NULL, REPLY_CS, (void *)&Rep[l]);
		if (rc)
		{
			cout<<"Problem with the creating Reply Thread.."<<endl;
			return 0;	
		}
	}
	

}
int main()
{	
	
	list <SocketWrapper> SocketConnectionList;
	
	int status=makeConnection(&SocketConnectionList);
	int stat=ReplyClient(&SocketConnectionList);
	while(1)
	{
	}
	
		
	return 0;
}

//////////////////////////




#include "connection.h"

using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t server_list_lock;
pthread_mutex_t Seq_num_lock;
pthread_mutex_t Reply_Deferred_lock;
pthread_mutex_t Highest_Seq_num_lock;
pthread_mutex_t A_lock;	
pthread_mutex_t Our_Priority_lock;
pthread_mutex_t struct_lock;
pthread_mutex_t Id_thread_lock;
int Id_thread=0;

int connect_complete=0; //global variable

string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
struct Client_send_data
{
	int client_num;
	list <SocketWrapper> *SocketConnectionList;
	list <SocketWrapper> *SocketConnectionListServer;
	int over_ride;

};
int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *end_msg = "end_connect";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	
	for(int i=0;i<3;i++)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=serverIPList[i];
		w1.connect_num=i;
		//connecting the clients in mesh
		int stat=s1.connect_socket(serverIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<serverIPList[i]<<endl;
				
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&server_list_lock);
			(data->SocketConnectionListServer)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&server_list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}	
	for(int i=4;i>(data->client_num);i--)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=clientIPList[i];
		w1.connect_num=findClientNum(string(clientIPList[i]));
		//connecting the clients in mesh
		int stat=s1.connect_socket(clientIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<clientIPList[i]<<endl;
			
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&list_lock);
			(data->SocketConnectionList)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}
	/*	
	cout<<"list ended.."<<endl;
	if(1)
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).sender<<" to "<<(*it).receiver<<endl;
	
		}
	}
	*/
	if(data->client_num==4)
	{
		list <SocketWrapper> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//if((*it)->sender==string(clientIPList[0]))								
			if(send(((*it).socket_id).return_accept_socket() , end_msg , strlen(end_msg) , 0 )<0)
			{
				cout<<"error in sending msg.."<<endl;	
			}
			cout<<"End message sent to "<< (*it).sender<<endl;
			//sleep(1);
								
		}		
		connect_complete=1;


	}
	else
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).receiver<<" to  "<<(*it).sender<<endl;
			if((*it).receiver==string(clientIPList[4]))
			{
				
				cout<<"here"<<endl;
				char buf[1024]={0};
				valread = read(((*it).socket_id).socket_fd, buf, 1024); 
				cout<<"wait for end msg from "<<(*it).receiver<<" to me "<<(*it).sender<<endl;
				if(valread && (strcmp(buf, "end_connect") == 0))
				{
					//received_pre_msg=1;						
					//cout<<string(buf)<<" #### "<<endl;
					connect_complete=1; 
					break;
				}
					
			}
		}
	}
		
	
}

int makeConnection(list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return 0;
					
		}
		
	}
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1.address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
	
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=SocketConnectionList;
	t.SocketConnectionListServer=SocketConnectionListServer;
	t.client_num=client_num;
	t.over_ride=0;
	rc=pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	
	while(!connect_complete);
	cout<<"Connection completed"<<endl;	
	
}

struct Request_thread_data
{
	int client_num;
	//list <SocketWrapper> *SocketConnectionList;
	//list <SocketWrapper> *SocketConnectionListServer;
	int *Seq_num;
	int socket;
	//int *Reply_expected;

};
struct Reply_thread_data
{
	int client_num;
	int Seq_num=0;
	int Highest_Seq_num;
	
	////int Reply_expected=N-1;
	////bool Critical_Section=FALSE;
	bool Reply_Deferred[5];
		
	bool Using;
	bool Waiting;
	bool A[5];
	int socket[5];

/*


	int client_num;
	int *Seq_num;
	int socket;
	//int *Reply_expected;
	bool *Reply_Deferred;
	int *Highest_Seq_num;
	//bool *Critical_Section;
	bool *A;
	bool *Using;
	bool *Waiting;
*/
};
void *REQUEST_CS(void *threadarg)
{
	struct Request_thread_data *data;
	data = (struct Request_thread_data *) threadarg;
	//cout<<data->client_num<<" "<<*data->Seq_num<<" "<<endl;
	char req_msg[100]={0};
	
	snprintf( req_msg, sizeof(req_msg), "%s:%d:%d", "REQ", data->client_num,*data->Seq_num);
	//cout<<req_msg<<"... msg sent"<<endl;
	send(data->socket, &req_msg , strlen(req_msg) , 0 );
	//char buf[100]={0};
	//int valread = read(data->socket, buf, 100); 
	//if(valread && (strcmp(buf, "REPLY") == 0))
	//{
	//	data->Reply_expected--;
	//	cout<<data->Reply_expected;
	//}
	
}
void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data *data;
	data = (struct Reply_thread_data *) threadarg;
	//cout<<data->client_num<<" "<<data->Seq_num<<" "<<endl;
	int Client_Req_num;// j
	int Client_Rep_num;
	int Req_Seq_num;//k
	char* reply_msg;
	char* temp;
	int temp_thread_id;
	pthread_mutex_lock(&Id_thread_lock);
	temp_thread_id=Id_thread;
	if(temp_thread_id==data->client_num)
	{
		Id_thread++;
		temp_thread_id=Id_thread;
	}
	Id_thread++;
	pthread_mutex_unlock(&Id_thread_lock);
	cout<<"My thread number "<<temp_thread_id<<endl;
	//bool Defer_it=FALSE;
	bool Our_Priority=FALSE;
	//int Highest_Seq_num=0;
	while(1)
	{	
		//cout<<"waiting for any req msg"<<endl;
		char buf[100]={0};
		
		int valread = read(data->socket[temp_thread_id], buf, 100); 
		string buffer(buf);
		//cout<<temp;
		size_t found = buffer.find("REPLY");
  		size_t found1 = buffer.find("REQ");
		
		if(valread && found!=string::npos)
		{
			
			//pthread_mutex_lock(&Reply_expected_lock);
			//*data->Reply_expected=*data->Reply_expected-1;
			//pthread_mutex_unlock(&Reply_expected_lock);
			temp=strtok(buf,":");
			temp=strtok(NULL,":");
			cout<<temp;
			Client_Rep_num=atoi(temp);
			pthread_mutex_lock(&struct_lock);
			data->A[Client_Rep_num]=TRUE;
			pthread_mutex_unlock(&struct_lock);
			//cout<<"got reply - waiting reply from "<<temp<<" still waiting for "<<*data->Reply_expected<<"replies"<<endl;
			valread=0;
		}
		else if (valread && found1!=string::npos)
		{
			reply_msg = strtok(buf, ":");
			reply_msg = strtok(NULL, ":"); 
	    		Client_Req_num=atoi(reply_msg); 
			reply_msg = strtok(NULL, ":");
			Req_Seq_num=atoi(reply_msg);
			
			pthread_mutex_lock(&struct_lock);
			data->Highest_Seq_num=max(data->Highest_Seq_num,Req_Seq_num);
			cout<<data->Highest_Seq_num<<" Highest seq num"<<endl;
			pthread_mutex_unlock(&struct_lock);
			
			if( (Req_Seq_num > data->Seq_num)  ||  ( (Req_Seq_num == data->Seq_num) && (Client_Req_num > data->client_num) ))
			{
				
				//pthread_mutex_lock(&Our_Priority_lock);
				Our_Priority=TRUE;
				//pthread_mutex_unlock(&Our_Priority_lock);
			}
			
			if(data->Using ||(data->Waiting && Our_Priority))
			{
				pthread_mutex_lock(&struct_lock);
				data->Reply_Deferred[Client_Req_num]=TRUE;
				pthread_mutex_unlock(&struct_lock);	
				cout<<"Defered reply to "<<Client_Req_num<<endl;
			}
			
			if(  !(data->Using || data->Waiting) || (data->Waiting && !(data->A[Client_Req_num]) && !(Our_Priority) ))
			{
				pthread_mutex_lock(&struct_lock);
				data->A[Client_Req_num]=FALSE;
				pthread_mutex_unlock(&struct_lock);
				char reply_msg[100]={0};	
				snprintf( reply_msg, sizeof(reply_msg), "%s:%d", "REPLY",data->client_num);
				cout<<"Sent Reply to "<<Client_Req_num<<endl;
				send(data->socket[temp_thread_id], &reply_msg , strlen(reply_msg) , 0 );
			}
			
			if(data->Waiting && data->A[Client_Req_num] && !Our_Priority)
			{
				pthread_mutex_lock(&struct_lock);
				data->A[Client_Req_num]=FALSE;
				pthread_mutex_unlock(&struct_lock);
				char reply_msg[100]={0};	
				snprintf( reply_msg, sizeof(reply_msg), "%s:%d", "REPLY",data->client_num);
				cout<<"Sent Reply to "<<Client_Req_num<<endl;
				send(data->socket[temp_thread_id], &reply_msg , strlen(reply_msg) , 0 );

				char req_msg[100]={0};
				snprintf( req_msg, sizeof(req_msg), "%s:%d:%d", "REQ", data->client_num,data->Seq_num);
				send(data->socket[temp_thread_id], &req_msg , strlen(req_msg) , 0 );
			}
			
		}
		//cout<<"reply exit.."<<*data->Reply_expected<<endl;
	
	}

}
int ricart_agrawala_algo(int N,int client_num,list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{
	srand (time(NULL));
	
	char reply[100]={0};
	char ServerReq[100]={0};	
	///thread
	pthread_t REPLY[4];
	struct Reply_thread_data Rep;
	Rep.Seq_num=0;
	Rep.Highest_Seq_num=0;
	Rep.Reply_Deferred[5]={0};
	Rep.client_num=client_num;
	Rep.Using=FALSE;
	Rep.Waiting=FALSE;
	for(int n=0;n<5;n++)
	{
		Rep.A[n]={FALSE};
		Rep.Reply_Deferred[n]={0};
	}
	//Rep.A[5]={FALSE};
	
	int l;
	list <SocketWrapper> :: iterator itt;
	for(l=0, itt = SocketConnectionList->begin(); itt != SocketConnectionList->end(); ++itt,l++) 
	{		
		
		if((*itt).connect_num>client_num)
		{
			Rep.socket[(*itt).connect_num]=((*itt).socket_id).socket_fd;
		}
		else
		{
			Rep.socket[(*itt).connect_num]=((*itt).socket_id).return_accept_socket();
		}
	
		
	}
	struct Reply_thread_data *RepPointer=&Rep;
	for(int r=0;r<5;r++)
	{
		if (r==client_num)
			continue;
		int rc = pthread_create(&REPLY[l], NULL, REPLY_CS, (void *)RepPointer);
		if (rc)
		{
			cout<<"Problem with the creating Reply Thread.."<<endl;
			return 0;	
		}
	}
	pthread_t REQ[5];
	struct Request_thread_data Req[5];
	int s;
	list <SocketWrapper> :: iterator it;


	for(int count=0;count<1;count++)
	//if(getIPAddress()==string(CLIENT0))
	{
		usleep(5000000);
		cout<<"Request entry to the Critical Section"<<endl;
		//Reply_expected=N-1;
		//A[5]={FALSE};
		//Critical_Section=TRUE;//not used in optimization
		Rep.Waiting=TRUE;
		Rep.Seq_num=Rep.Highest_Seq_num+1;
		
		for(s=0, it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it,s++) 
		{
			
			Req[s].client_num=client_num;
			Req[s].Seq_num=&Rep.Seq_num;
			//Req[s].Reply_expected=&Reply_expected;
			if((*it).connect_num>client_num)
			{
				Req[s].socket=((*it).socket_id).socket_fd;
			}
			else
			{
				Req[s].socket=((*it).socket_id).return_accept_socket();
			}
			
			int rc = pthread_create(&REQ[s], NULL, REQUEST_CS, (void *)&Req[s]);
			if (rc)
			{
				cout<<"Problem with the creating Request Thread.."<<endl;
				return 0;	
			}

		}
		for(int i=0;i<N;i++)   //optimization waiting
		{	
			if(i==client_num)
				continue;
			while(!Rep.A[i]);
						
		}
		Rep.Waiting=FALSE;
		Rep.Using=TRUE;
		//while(!Reply_expected==0);
		cout<<"PERFORM CRITICAL SECTION"<<endl;
		//srand (time(NULL));
		int server_no=rand() % 3;//contact a random server to write to file
		cout<<"Connecting to Random Server "<<server_no<<endl;;
		list <SocketWrapper> :: iterator it1;
		
		for(it1 = SocketConnectionListServer->begin(); it1 != SocketConnectionListServer->end(); ++it1) 
		{
			//cout<<(*it1).connect_num<<endl;
			if((*it1).connect_num==server_no)
			{
				//cout<<"req sent to server"<<endl;
				snprintf( ServerReq, sizeof(ServerReq), "%s:%d:%d", "WRITE",client_num,Rep.Seq_num);
				send(((*it1).socket_id).socket_fd,&ServerReq , strlen(ServerReq) , 0);
				char buf1[100]={0};
				//sleep(1);
				int valread = read(((*it1).socket_id).socket_fd, buf1, 100); 
				string buffer1(buf1);
				cout<<"From server "<<buf1<<endl;
				size_t found = buffer1.find("finish");
  				
				if(valread && found!=string::npos)
				{
					cout<<"done with Critical Section"<<endl;
				}
				//break;
			}
		}
		Rep.Using=FALSE;
		//Critical_Section=FALSE;
		for(int i=0;i<N;i++)
		{
			if(Rep.Reply_Deferred[i]==TRUE)
			{
				Rep.A[i]=FALSE;
				Rep.Reply_Deferred[i]=FALSE;
				for(it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it) 
				{
					if((*it).connect_num==i)
					{
						snprintf( reply, sizeof(reply), "%s:%d", "REPLY",client_num);
						if(i>client_num)
						{
							send(((*it).socket_id).socket_fd,&reply , strlen(reply) , 0);
						}
						else
						{
							send(((*it).socket_id).return_accept_socket(),&reply , strlen(reply) , 0);
						}
						cout<<"Sent Deferred Reply to "<<i<<endl;
					}
				}
			}
		}
	}

}

int main()
{	
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num;
	client_num=findClientNum(getIPAddress());
	cout<<client_num;
	//pthread_t ClientThread;
	list <SocketWrapper> SocketConnectionList;
	list <SocketWrapper> SocketConnectionListServer;
	int status=makeConnection(&SocketConnectionList,&SocketConnectionListServer);
	cout<<"*****************Connection Established******************"<<endl;
	int stat=ricart_agrawala_algo(N,client_num,&SocketConnectionList,&SocketConnectionListServer);
	cout<<"**DONE**"<<endl;
	while(1)
	{
	}
	
		
	return 0;
}
///////// ser



#include <ifaddrs.h>
#include "connection.h"
#include <thread>
using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
//pthread_mutex_t client_lock;


string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
/*
struct Client_send_data
{
	int client_num;
	list <SocketWrapper*> *SocketConnectionList;
	int over_ride;
	
	
};
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char buffer[1024] = {0};
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	//pthread_mutex_lock(data->client_lock);
	if (data->over_ride==0)
	{
		
		cout<<"wait for before client to connect and send data #"<<data->client_num<<endl;
		
		while(1)
		{	
			list <SocketWrapper*> :: iterator i;
			//pthread_mutex_lock(&list_lock);
				
			for(i = (data->SocketConnectionList)->begin(); i != (data->SocketConnectionList)->end(); ++i) 
			{	
				if((*i)->sender==clientIPList[(data->client_num)-1] && (*i)->receiver==clientIPList[data->client_num])
				{
					cout<<"Setup message received from "<< clientIPList[(data->client_num)-1]<<endl;
					valread = read(((*i)->socket_id)->return_accept_response()  , buffer, 1024); 
					//cout<<buffer<<endl<<" #### "<<endl;
	    				if(valread && (strcmp(buffer, "start") == 0))
					{
						received_pre_msg=1;						
						cout<<string(buffer)<<endl<<" #### "<<endl; 
						
					}
				}
			}
			
			//pthread_mutex_unlock(&list_lock);
			
			if(received_pre_msg)
			{
				break;
			}

			
		}
	    		
	}
	if (received_pre_msg or data->over_ride==1)
	{	
		for(int i=(data->client_num)+1;i<5;i++)
		{
		
			Socket_connection* s1=new Socket_connection();  //created for the sender clients
			SocketWrapper* w1=new SocketWrapper();
			w1->sender=getIPAddress();
			w1->receiver=clientIPList[i];
			//connecting the clients in mesh
			int stat=s1->connect_socket(clientIPList[i]);
			w1->socket_id=s1;
			if (stat==1)
			{	
				pthread_mutex_lock(&list_lock);
				data->SocketConnectionList->push_front(w1); //need mutex here
				pthread_mutex_unlock(&list_lock);
				cout<<"Sender - connected "<< w1->sender <<" to "<< w1->receiver <<endl;
			}
			else
			{
				cout<<"error in sending the client connect.."<<endl;
			
			}
			
			
			//list <SocketWrapper*> :: iterator it; 
			//for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
			//{
			//	cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
			//}
			
	
		}
	
	
		list <SocketWrapper*> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//cout << "check---conection from " << (*it)->sender <<" to "<< (*it)->receiver <<" established"<<endl; 
		
			if((*it)->sender==clientIPList[data->client_num] && (*it)->receiver==clientIPList[(data->client_num)+1])
			{
					
				send(((*it)->socket_id)->socket_fd , setup_msg , strlen(setup_msg) , 0 ); 
				cout<<"Setup message sent to "<< clientIPList[(data->client_num)+1]<<endl;
				break;
			}
		
		}
		
		
	}
	cout<<"list ended.."<<endl;
	
}
*/


int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}
int writeFile(char* filename,char* appendText)
{
	FILE *file;
	file=fopen(filename,"a");
	if(file==NULL)
	{
		cout<<"error in opening the file"<<endl;
		return 0;
	}
	fprintf(file,"%s\n",appendText);
	fclose(file);
	return 1;

}
string getLastLine(string filename)
{
	
	ifstream fin;
	string lastLine;
	fin.open(filename);
    	if(fin.is_open()) 
	{
        	fin.seekg(-1,ios_base::end);                // go to one spot before the EOF

        	bool keepLooping = true;
        	while(keepLooping) 
		{
            		char ch;
            		fin.get(ch);                            // Get current byte's data

            		if((int)fin.tellg() <= 1) 
			{             // If the data was at or before the 0th byte
                		fin.seekg(0);                       // The first line is the last line
                		keepLooping = false;                // So stop there
            		}
            		else if(ch == '\n') 
			{                   // If the data was a newline
                		keepLooping = false;                // Stop at the current position.
				
            		}
            		else 
			{                                  // If the data was neither a newline nor at the 0 byte
                		fin.seekg(-2,ios_base::cur);        // Move to the front of that data
            		}
        	}

                    
        getline(fin,lastLine);                      // Read the current line
        cout << "Result: " << lastLine << '\n';     // Display it

        fin.close();
	}
	return lastLine;
}
int makeConnection(list <SocketWrapper> *SocketConnectionList)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (SocketConnectionList->size()==5)
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
		
	cout<<"Connection completed"<<endl;	
	
}
struct Reply_thread_data
{
	int socket;
	int client_num;
};

void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data *data;
	data = (struct Reply_thread_data *) threadarg;
	char finish_msg[100] ={0};
	while(1)
	{
		cout<<"waiting for any req msg from clients"<<endl;
		char buf[100]={0};
		
		int valread = read(data->socket, buf, 100); 
		//string buffer(buf);
		cout<<"message received "<<buf<<" from "<<data->client_num<<endl;
		/// execute the critical section here
		writeFile("file1.txt",buf);
		string lastLine=getLastLine("file1.txt");
		cout<<"read the last data"<<lastLine<<endl;
		/// critical section
		snprintf( finish_msg, sizeof(finish_msg), "%s:%d", "WRITE",data->client_num);
		send(data->socket, &finish_msg , strlen(finish_msg) , 0 );
	}

}
int ReplyClient(list <SocketWrapper> *SocketConnectionList)
{
	pthread_t REPLY[5];
	struct Reply_thread_data Rep[5];
	int l;
	list <SocketWrapper> :: iterator itt;
	for(l=0, itt = SocketConnectionList->begin(); itt != SocketConnectionList->end(); ++itt,l++) 
	{
		Rep[l].socket=((*itt).socket_id).return_accept_socket();
		Rep[l].client_num=(*itt).connect_num;
		int rc = pthread_create(&REPLY[l], NULL, REPLY_CS, (void *)&Rep[l]);
		if (rc)
		{
			cout<<"Problem with the creating Reply Thread.."<<endl;
			return 0;	
		}
	}
	

}
int main()
{	
	
	list <SocketWrapper> SocketConnectionList;
	
	int status=makeConnection(&SocketConnectionList);
	int stat=ReplyClient(&SocketConnectionList);
	while(1)
	{
	}
	
		
	return 0;
}




//////////////////////good edit not tested

#include "connection.h"

using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t server_list_lock;
pthread_mutex_t Seq_num_lock;
pthread_mutex_t Reply_Deferred_lock;
pthread_mutex_t Highest_Seq_num_lock;
pthread_mutex_t A_lock;	
pthread_mutex_t Our_Priority_lock;
pthread_mutex_t struct_lock;
pthread_mutex_t Id_thread_lock;
int Id_thread=0;

int connect_complete=0; //global variable

string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
struct Client_send_data
{
	int client_num;
	list <SocketWrapper> *SocketConnectionList;
	list <SocketWrapper> *SocketConnectionListServer;
	int over_ride;

};
int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *end_msg = "end_connect";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	
	for(int i=0;i<3;i++)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=serverIPList[i];
		w1.connect_num=i;
		//connecting the clients in mesh
		int stat=s1.connect_socket(serverIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<serverIPList[i]<<endl;
				
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&server_list_lock);
			(data->SocketConnectionListServer)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&server_list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}	
	for(int i=4;i>(data->client_num);i--)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=clientIPList[i];
		w1.connect_num=findClientNum(string(clientIPList[i]));
		//connecting the clients in mesh
		int stat=s1.connect_socket(clientIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<clientIPList[i]<<endl;
			
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&list_lock);
			(data->SocketConnectionList)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}
	/*	
	cout<<"list ended.."<<endl;
	if(1)
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).sender<<" to "<<(*it).receiver<<endl;
	
		}
	}
	*/
	if(data->client_num==4)
	{
		list <SocketWrapper> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//if((*it)->sender==string(clientIPList[0]))								
			if(send(((*it).socket_id).return_accept_socket() , end_msg , strlen(end_msg) , 0 )<0)
			{
				cout<<"error in sending msg.."<<endl;	
			}
			cout<<"End message sent to "<< (*it).sender<<endl;
			//sleep(1);
								
		}		
		connect_complete=1;


	}
	else
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).receiver<<" to  "<<(*it).sender<<endl;
			if((*it).receiver==string(clientIPList[4]))
			{
				
				cout<<"here"<<endl;
				char buf[1024]={0};
				valread = read(((*it).socket_id).socket_fd, buf, 1024); 
				cout<<"wait for end msg from "<<(*it).receiver<<" to me "<<(*it).sender<<endl;
				if(valread && (strcmp(buf, "end_connect") == 0))
				{
					//received_pre_msg=1;						
					//cout<<string(buf)<<" #### "<<endl;
					connect_complete=1; 
					break;
				}
					
			}
		}
	}
		
	
}

int makeConnection(list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return 0;
					
		}
		
	}
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1.address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
	
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=SocketConnectionList;
	t.SocketConnectionListServer=SocketConnectionListServer;
	t.client_num=client_num;
	t.over_ride=0;
	rc=pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	
	while(!connect_complete);
	cout<<"Connection completed"<<endl;	
	
}


list <char*> askServerFile(list <SocketWrapper> *SocketConnectionListServer,int client_num)
{
	int random_server=rand()%3;

	char req_msg[100]={0};
	
	//snprintf( req_msg, sizeof(req_msg), "%s:%d:%d", "ENQUIRE", data->client_num);
	//send(data->socket, &req_msg , strlen(req_msg) , 0 );
	list <char*> files;
	list <SocketWrapper> :: iterator it1;
		
	for(it1 = SocketConnectionListServer->begin(); it1 != SocketConnectionListServer->end(); ++it1) 
	{
		//cout<<(*it1).connect_num<<endl;
		if((*it1).connect_num==random_server)
		{
			//cout<<"req sent to server"<<endl;
			snprintf( req_msg, sizeof(req_msg), "%s:%d", "ENQUIRE",client_num);
			send(((*it1).socket_id).socket_fd,&req_msg , strlen(req_msg) , 0);
			char buf1[100]={0};
			//sleep(1);
			int valread = read(((*it1).socket_id).socket_fd, buf1, 100); 
			//string buffer1(buf1);
			cout<<"From server "<<buf1<<endl;
			char *token = strtok(buf1,",");
    			while (token)
    			{
        			//cout << token << endl;
				files.push_back(token);
        			token = strtok(NULL,",");
    			}
			break;
		}
	}
	return files;
}


struct Request_thread_data
{
	int client_num;
	//list <SocketWrapper> *SocketConnectionList;
	//list <SocketWrapper> *SocketConnectionListServer;
	int *Seq_num;
	int socket;
	//int *Reply_expected;
	int resource_num;

};
struct Reply_thread_data_array
{
	struct Reply_thread_data *Reply;
};
struct Reply_thread_data
{
	int client_num;
	int Seq_num=0;
	int Highest_Seq_num;
	bool Reply_Deferred[5];
	bool Using;
	bool Waiting;
	bool A[5];
	int socket[5];

};
void *REQUEST_CS(void *threadarg)
{
	struct Request_thread_data *data;
	data = (struct Request_thread_data *) threadarg;
	
	char req_msg[100]={0};
	
	snprintf( req_msg, sizeof(req_msg), "%s:%d:%d:%d", "REQ", data->client_num,*data->Seq_num,data->resource_num);

	send(data->socket, &req_msg , strlen(req_msg) , 0 );
	
}
void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data_array *data;
	data = (struct Reply_thread_data_array *) threadarg;
	int resource_num;
	int Client_Req_num;// j
	int Client_Rep_num;
	int Req_Seq_num;//k
	char* reply_msg;
	char* temp;
	int temp_thread_id;
	pthread_mutex_lock(&Id_thread_lock);
	temp_thread_id=Id_thread;
	if(temp_thread_id==data->Reply->client_num)
	{
		Id_thread++;
		temp_thread_id=Id_thread;
	}
	Id_thread++;
	pthread_mutex_unlock(&Id_thread_lock);
	//cout<<"My thread number "<<temp_thread_id<<endl;
	
	bool Our_Priority=FALSE;
	//cout<<"test point"<<(data->Reply)[1].Seq_num<<endl;

	while(1)
	{	
		//cout<<"waiting for any req msg"<<endl;
		char buf[100]={0};
		
		int valread = read(data->Reply->socket[temp_thread_id], buf, 100); 
		string buffer(buf);
		//cout<<temp;
		size_t found = buffer.find("REPLY");
  		size_t found1 = buffer.find("REQ");
		
		if(valread && found!=string::npos)
		{
			
			temp=strtok(buf,":");
			temp=strtok(NULL,":");
			cout<<temp;
			Client_Rep_num=atoi(temp);
			temp=strtok(NULL,":");
			resource_num=atoi(temp);
			pthread_mutex_lock(&struct_lock);
			(data->Reply)[resource_num].A[Client_Rep_num]=TRUE;
			pthread_mutex_unlock(&struct_lock);
			//cout<<"got reply - waiting reply from "<<temp<<" still waiting for "<<*data->Reply_expected<<"replies"<<endl;
			valread=0;
		}
		else if (valread && found1!=string::npos)
		{
			reply_msg = strtok(buf, ":");
			reply_msg = strtok(NULL, ":"); 
	    		Client_Req_num=atoi(reply_msg); 
			reply_msg = strtok(NULL, ":");
			Req_Seq_num=atoi(reply_msg);
			reply_msg = strtok(NULL, ":");
			resource_num=atoi(reply_msg);
			pthread_mutex_lock(&struct_lock);
			(data->Reply)[resource_num].Highest_Seq_num=max((data->Reply)[resource_num].Highest_Seq_num,Req_Seq_num);
			//cout<<(data->Reply)[resource_num].Highest_Seq_num<<" Highest seq num"<<endl;
			pthread_mutex_unlock(&struct_lock);
			
			if( (Req_Seq_num > (data->Reply)[resource_num].Seq_num)  ||  ( (Req_Seq_num == (data->Reply)[resource_num].Seq_num) && (Client_Req_num > (data->Reply)[resource_num].client_num) ))
			{
				
				//pthread_mutex_lock(&Our_Priority_lock);
				Our_Priority=TRUE;
				//pthread_mutex_unlock(&Our_Priority_lock);
			}
			
			if((data->Reply)[resource_num].Using ||((data->Reply)[resource_num].Waiting && Our_Priority))
			{
				pthread_mutex_lock(&struct_lock);
				(data->Reply)[resource_num].Reply_Deferred[Client_Req_num]=TRUE;
				pthread_mutex_unlock(&struct_lock);	
				cout<<"Defered reply to "<<Client_Req_num<<endl;
			}
			
			if(  !((data->Reply)[resource_num].Using || (data->Reply)[resource_num].Waiting) || ((data->Reply)[resource_num].Waiting && !((data->Reply)[resource_num].A[Client_Req_num]) && !(Our_Priority) ))
			{
				pthread_mutex_lock(&struct_lock);
				(data->Reply)[resource_num].A[Client_Req_num]=FALSE;
				pthread_mutex_unlock(&struct_lock);
				char reply_msg[100]={0};	
				snprintf( reply_msg, sizeof(reply_msg), "%s:%d:%d", "REPLY",(data->Reply)[resource_num].client_num,resource_num);
				cout<<"Sent Reply to "<<Client_Req_num<<endl;
				send((data->Reply)[resource_num].socket[temp_thread_id], &reply_msg , strlen(reply_msg) , 0 );
			}
			
			if((data->Reply)[resource_num].Waiting && (data->Reply)[resource_num].A[Client_Req_num] && !Our_Priority)
			{
				pthread_mutex_lock(&struct_lock);
				(data->Reply)[resource_num].A[Client_Req_num]=FALSE;
				pthread_mutex_unlock(&struct_lock);
				char reply_msg[100]={0};	
				snprintf( reply_msg, sizeof(reply_msg), "%s:%d:%d", "REPLY",(data->Reply)[resource_num].client_num,resource_num);
				cout<<"Sent Reply to "<<Client_Req_num<<endl;
				send(data->Reply->socket[temp_thread_id], &reply_msg , strlen(reply_msg) , 0 );

				char req_msg[100]={0};
				snprintf( req_msg, sizeof(req_msg), "%s:%d:%d:%d", "REQ", (data->Reply)[resource_num].client_num,(data->Reply)[resource_num].Seq_num,resource_num);
				send((data->Reply)[resource_num].socket[temp_thread_id], &req_msg , strlen(req_msg) , 0 );
			}
			
		}
		//cout<<"reply exit.."<<*data->Reply_expected<<endl;
	
	}

}
int ricart_agrawala_algo(int N,int client_num,list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer,list <char*> *ServerFiles)
{
	
	
	char reply[100]={0};
	char ServerReq[100]={0};	
	///thread
	pthread_t REPLY[4];
	struct Reply_thread_data *Rep=(struct Reply_thread_data *)malloc(sizeof(struct Reply_thread_data)*ServerFiles->size());
	
	for(int f=0;f<ServerFiles->size();f++)
	{
		Rep[f].Seq_num=0;
		Rep[f].Highest_Seq_num=0;
		
		Rep[f].client_num=client_num;
		Rep[f].Using=FALSE;
		Rep[f].Waiting=FALSE;
		for(int n=0;n<5;n++)
		{
			Rep[f].A[n]={FALSE};
			Rep[f].Reply_Deferred[n]={0};
		}
	
		int l;
		list <SocketWrapper> :: iterator itt;
		for(l=0, itt = SocketConnectionList->begin(); itt != SocketConnectionList->end(); ++itt,l++) 
		{		
		
			if((*itt).connect_num>client_num)
			{
				Rep[f].socket[(*itt).connect_num]=((*itt).socket_id).socket_fd;
			}
			else
			{
				Rep[f].socket[(*itt).connect_num]=((*itt).socket_id).return_accept_socket();
			}
			
		}
	}
	struct Reply_thread_data_array RepPointer;
	RepPointer.Reply=Rep;
	for(int r=0;r<5;r++)
	{
		if (r==client_num)
			continue;
		int rc = pthread_create(&REPLY[r], NULL, REPLY_CS, (void *)&RepPointer);
		if (rc)
		{
			cout<<"Problem with the creating Reply Thread.."<<endl;
			return 0;	
		}
	}
	pthread_t REQ[5];
	struct Request_thread_data Req[5];
	int s;
	list <SocketWrapper> :: iterator it;


	for(int count=0;count<1;count++)
	
	{
		int sleep_time=rand()%10;
		cout<<"My Sleep time is "<<sleep_time<<" sec "<<endl;
		usleep(sleep_time*1000000);
		int resource_file=rand()%ServerFiles->size();
		int R_W=rand()%2;
		if(R_W==1)
			cout<<"Requesting to write file "<<*next(ServerFiles->begin(),resource_file)<<endl;
		else
			cout<<"Requesting to read file "<<*next(ServerFiles->begin(),resource_file)<<endl;
		cout<<"Request entry to the Critical Section"<<endl;
		
		Rep[resource_file].Waiting=TRUE;
		Rep[resource_file].Seq_num=Rep[resource_file].Highest_Seq_num+1;
		
		for(s=0, it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it,s++) 
		{
			
			Req[s].client_num=client_num;
			Req[s].Seq_num=&Rep[resource_file].Seq_num;
			Req[s].resource_num=resource_file;
			if((*it).connect_num>client_num)
			{
				Req[s].socket=((*it).socket_id).socket_fd;
			}
			else
			{
				Req[s].socket=((*it).socket_id).return_accept_socket();
			}
			
			int rc = pthread_create(&REQ[s], NULL, REQUEST_CS, (void *)&Req[s]);
			if (rc)
			{
				cout<<"Problem with the creating Request Thread.."<<endl;
				return 0;	
			}

		}
		for(int i=0;i<N;i++)   //optimization waiting
		{	
			if(i==client_num)
				continue;
			while(!Rep[resource_file].A[i]);
						
		}
		Rep[resource_file].Waiting=FALSE;
		Rep[resource_file].Using=TRUE;
		//while(!Reply_expected==0);
		cout<<"PERFORM CRITICAL SECTION"<<endl;
		//srand (time(NULL));
		int server_no=rand() % 3;//contact a random server to write to file
		cout<<"Connecting to Random Server "<<server_no<<endl;;
		list <SocketWrapper> :: iterator it1;
		if (R_W==1)
		{
			for(it1 = SocketConnectionListServer->begin(); it1 != SocketConnectionListServer->end(); ++it1) 
			{
			
				snprintf( ServerReq, sizeof(ServerReq), "%s:%s", "WRITE",*next(ServerFiles->begin(),resource_file));
				send(((*it1).socket_id).socket_fd,&ServerReq , strlen(ServerReq) , 0);
				char buf1[100]={0};
				//sleep(1);
				int valread = read(((*it1).socket_id).socket_fd, buf1, 100); 
				string buffer1(buf1);
				cout<<"From server "<<buf1<<endl;
				size_t found = buffer1.find("finish");
  				
				if(valread && found!=string::npos)
				{
					cout<<"done with Critical Section"<<endl;
				}
					
				
			}
		}
		else
		{
			for(it1 = SocketConnectionListServer->begin(); it1 != SocketConnectionListServer->end(); ++it1) 
			{
				
				if((*it1).connect_num==server_no)
				{
					//cout<<"req sent to server"<<endl;
					snprintf( ServerReq, sizeof(ServerReq), "%s:%s", "READ",*next(ServerFiles->begin(),resource_file));
					send(((*it1).socket_id).socket_fd,&ServerReq , strlen(ServerReq) , 0);
					char buf1[100]={0};
					//sleep(1);
					int valread = read(((*it1).socket_id).socket_fd, buf1, 100); 
					string buffer1(buf1);
					cout<<"From server "<<buf1<<endl;
					
				}
			}
		}

		Rep[resource_file].Using=FALSE;
		//Critical_Section=FALSE;
		for(int i=0;i<N;i++)
		{
			if(Rep[resource_file].Reply_Deferred[i]==TRUE)
			{
				Rep[resource_file].A[i]=FALSE;
				Rep[resource_file].Reply_Deferred[i]=FALSE;
				for(it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it) 
				{
					if((*it).connect_num==i)
					{
						snprintf( reply, sizeof(reply), "%s:%d", "REPLY",client_num);
						if(i>client_num)
						{
							send(((*it).socket_id).socket_fd,&reply , strlen(reply) , 0);
						}
						else
						{
							send(((*it).socket_id).return_accept_socket(),&reply , strlen(reply) , 0);
						}
						cout<<"Sent Deferred Reply to "<<i<<endl;
					}
				}
			}
		}
	}

}

int main()
{	
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num;
	srand (time(NULL));
	client_num=findClientNum(getIPAddress());
	cout<<client_num;
	//pthread_t ClientThread;
	list <SocketWrapper> SocketConnectionList;
	list <SocketWrapper> SocketConnectionListServer;
	list <char*> ServerFiles;
	int status=makeConnection(&SocketConnectionList,&SocketConnectionListServer);
	cout<<"*****************Connection Established******************"<<endl;
	ServerFiles=askServerFile(&SocketConnectionListServer,client_num);
	
	int stat=ricart_agrawala_algo(N,client_num,&SocketConnectionList,&SocketConnectionListServer,&ServerFiles);
	cout<<"**DONE**"<<endl;
	while(1)
	{
	}
	
		
	return 0;
}

//////good serv not tested

#include <ifaddrs.h>
#include "connection.h"
#include <thread>
using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t resource_lock;
//pthread_mutex_t client_lock;


string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
char* read_directory(char* name)
{
	//cout<<"111"<<endl;	
	char files[1024];
	DIR* dirp = opendir(name);
	//cout<<"222"<<endl;
	struct dirent * dp;
	//cout<<"333"<<endl;
	while ((dp = readdir(dirp)) != NULL) 
	{
	//cout<<"333"<<endl;
		if(strcmp(dp->d_name,".")==0 || strcmp(dp->d_name,"..")==0)
			continue;
		strncat(files,dp->d_name,strlen(dp->d_name));
		//cout<<files<<endl;
		strncat(files,",",strlen(","));
	}
	closedir(dirp);
	return files;
}
int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}
int writeFile(char* filename,char* appendText)
{
	FILE *file;
	file=fopen(filename,"a");
	if(file==NULL)
	{
		cout<<"error in opening the file"<<endl;
		return 0;
	}
	fprintf(file,"%s\n",appendText);
	fclose(file);
	return 1;

}
string getLastLine(char* filename)
{
	
	ifstream fin;
	string lastLine;
	fin.open(filename);
    	if(fin.is_open()) 
	{
        	fin.seekg(-1,ios_base::end);                // go to one spot before the EOF

        	bool keepLooping = true;
        	while(keepLooping) 
		{
            		char ch;
            		fin.get(ch);                            // Get current byte's data

            		if((int)fin.tellg() <= 1) 
			{             // If the data was at or before the 0th byte
                		fin.seekg(0);                       // The first line is the last line
                		keepLooping = false;                // So stop there
            		}
            		else if(ch == '\n') 
			{                   // If the data was a newline
                		keepLooping = false;                // Stop at the current position.
				
            		}
            		else 
			{                                  // If the data was neither a newline nor at the 0 byte
                		fin.seekg(-2,ios_base::cur);        // Move to the front of that data
            		}
        	}

                    
        getline(fin,lastLine);                      // Read the current line
        cout << "Result: " << lastLine << '\n';     // Display it

        fin.close();
	}
	return lastLine;
}
int makeConnection(list <SocketWrapper> *SocketConnectionList)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (SocketConnectionList->size()==5)
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
		
	cout<<"Connection completed"<<endl;	
	
}


struct Reply_thread_data
{
	int socket;
	int client_num;
};

void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data *data;
	data = (struct Reply_thread_data *) threadarg;
	char finish_msg[100] ={0};
	char* dir="/home/012/j/jx/jxb162030/Desktop/aos/0";
	while(1)
	{
		cout<<"waiting for any req msg from clients"<<endl;
		char buf[100]={0};
		
		int valread = read(data->socket, buf, 100); 
		//string buffer(buf);
		cout<<"message received "<<buf<<" from "<<data->client_num<<endl;
		/// execute the critical section here
		pthread_mutex_lock(&resource_lock);
		char *token = strtok(buf,":");
		if(strcmp(token,"ENQUIRE")==0)
		{	
			cout<<"Enquiring data from the directory"<<endl;
			int client;
    			
        		cout << token << endl;
			token = strtok(NULL,":");
			client=atoi(token);
    			
			cout<<"came out of loop"<<endl;
			//string dir="/home/012/j/jx/jxb162030/Desktop/aos/0";
			char* ServerFiles=read_directory(dir);
			send(data->socket, ServerFiles , strlen(ServerFiles) , 0 );

		}
		else if(strcmp(token,"WRITE")==0)
		{
			token = strtok(NULL,":");
			char* temp;
			temp="/home/012/j/jx/jxb162030/Desktop/aos/0/";
			strncat(temp,token,strlen(token));
			writeFile(temp,buf);
			//string lastLine=getLastLine("file1.txt");
			//cout<<"read the last data"<<lastLine<<endl;
			/// critical section
			snprintf( finish_msg, sizeof(finish_msg), "%s", "message written succesfully on file ",temp);
			send(data->socket, &finish_msg , strlen(finish_msg) , 0 );
		}
		else if(strcmp(token,"READ")==0)
		{
			token = strtok(NULL,":");
			char* temp;
			temp="/home/012/j/jx/jxb162030/Desktop/aos/0/";
			strncat(temp,token,strlen(token));
			string lastLine=getLastLine(temp);
			cout<<"read the last data"<<lastLine<<endl;
			snprintf( finish_msg, sizeof(finish_msg), "%s", lastLine.c_str());
			send(data->socket, &finish_msg , strlen(finish_msg) , 0 );
		}
		pthread_mutex_unlock(&resource_lock);
	}

}
int ReplyClient(list <SocketWrapper> *SocketConnectionList)
{
	pthread_t REPLY[5];
	struct Reply_thread_data Rep[5];
	int l;
	list <SocketWrapper> :: iterator itt;
	for(l=0, itt = SocketConnectionList->begin(); itt != SocketConnectionList->end(); ++itt,l++) 
	{
		Rep[l].socket=((*itt).socket_id).return_accept_socket();
		Rep[l].client_num=(*itt).connect_num;
		int rc = pthread_create(&REPLY[l], NULL, REPLY_CS, (void *)&Rep[l]);
		if (rc)
		{
			cout<<"Problem with the creating Reply Thread.."<<endl;
			return 0;	
		}
	}
	

}
int main()
{	
	
	list <SocketWrapper> SocketConnectionList;
	
	int status=makeConnection(&SocketConnectionList);
	int stat=ReplyClient(&SocketConnectionList);
	while(1)
	{
	}
	
		
	return 0;
}




///////////////final project



#include "connection.h"

using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t server_list_lock;
pthread_mutex_t Seq_num_lock;
pthread_mutex_t Reply_Deferred_lock;
pthread_mutex_t Highest_Seq_num_lock;
pthread_mutex_t A_lock;	
pthread_mutex_t Our_Priority_lock;
pthread_mutex_t struct_lock;
pthread_mutex_t Id_thread_lock;
int Id_thread=0;

int connect_complete=0; //global variable

string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
/*
struct thread_data 
{
	list <SocketWrapper*> *SocketConnectionList;
};
*/
struct Client_send_data
{
	int client_num;
	list <SocketWrapper> *SocketConnectionList;
	list <SocketWrapper> *SocketConnectionListServer;
	int over_ride;

};
int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}
void *SendClientConnection(void *threadarg)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};	
	struct Client_send_data *data;
	data = (struct Client_send_data *) threadarg;
	char *end_msg = "end_connect";
	char *setup_msg = "start";
	int valread=0;
	int received_pre_msg=0;	
	
	for(int i=0;i<3;i++)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=serverIPList[i];
		w1.connect_num=i;
		//connecting the clients in mesh
		int stat=s1.connect_socket(serverIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<serverIPList[i]<<endl;
				
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&server_list_lock);
			(data->SocketConnectionListServer)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&server_list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}	
	for(int i=4;i>(data->client_num);i--)//(int i=(data->client_num)+1;i<5;i++)
	{
		//sleep(1);
		Socket_connection s1;//=new Socket_connection();  //created for the sender clients
		SocketWrapper w1;//=new SocketWrapper();
		w1.sender=getIPAddress();
		w1.receiver=clientIPList[i];
		w1.connect_num=findClientNum(string(clientIPList[i]));
		//connecting the clients in mesh
		int stat=s1.connect_socket(clientIPList[i]);
		w1.socket_id=s1;
		if (stat==1)
		{	
			//cout<<"verfyng connection from "<<clientIPList[i]<<endl;
			
			char buf[1024]={0};
			valread = read(s1.socket_fd  , buf, 1024); 
			
			if(valread && (strcmp(buf, "received") == 0))
			{
				//received_pre_msg=1;						
				//cout<<string(buf)<<" #### "<<endl; 
				//break;
			
			
			pthread_mutex_lock(&list_lock);
			(data->SocketConnectionList)->push_back(w1); //need mutex here
			pthread_mutex_unlock(&list_lock);
			cout<<"Sender - connected "<< w1.sender <<" to "<< w1.receiver <<endl;
			}
		}
		else
		{
			cout<<"error in sending the client connect.."<<endl;
		
		}
		

	}
	
	if(data->client_num==4)
	{
		list <SocketWrapper> :: iterator it;	
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			//if((*it)->sender==string(clientIPList[0]))								
			if(send(((*it).socket_id).return_accept_socket() , end_msg , strlen(end_msg) , 0 )<0)
			{
				cout<<"error in sending msg.."<<endl;	
			}
			cout<<"End message sent to "<< (*it).sender<<endl;
			
								
		}		
		connect_complete=1;


	}
	else
	{	
		list <SocketWrapper> :: iterator it;
		for(it = (data->SocketConnectionList)->begin(); it != (data->SocketConnectionList)->end(); ++it) 
		{
			cout<<"reading list "<<(*it).receiver<<" to  "<<(*it).sender<<endl;
			if((*it).receiver==string(clientIPList[4]))
			{
				
				cout<<"here"<<endl;
				char buf[1024]={0};
				valread = read(((*it).socket_id).socket_fd, buf, 1024); 
				cout<<"wait for end msg from "<<(*it).receiver<<" to me "<<(*it).sender<<endl;
				if(valread && (strcmp(buf, "end_connect") == 0))
				{
					//received_pre_msg=1;						
					//cout<<string(buf)<<" #### "<<endl;
					connect_complete=1; 
					break;
				}
					
			}
		}
	}
		
	
}

int makeConnection(list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer)
{	
	cout<<"Main Client Thread created"<<endl;
	//struct thread_data *Client_data;
	//Client_data = (struct thread_data *) threadarg;
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	for(int i=0;i<5;i++)// find the 
	{
		if(getIPAddress()==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	
	if (client_num==0)
	{	
		cout<<"Enter 1 to setup client connection: ";
		cin>>connection_start;
		
		if (!(connection_start==1))
		{	
			cout<<"invalid parameter.. enter 1 to setup connecction ..exiting...";
			return 0;
					
		}
		
	}
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (string(clientIPList[client_num-1])==string(inet_ntoa(s1.address.sin_addr)))
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
	
	pthread_t SendConnectThread; 
	struct Client_send_data t;				
	t.SocketConnectionList=SocketConnectionList;
	t.SocketConnectionListServer=SocketConnectionListServer;
	t.client_num=client_num;
	t.over_ride=0;
	rc=pthread_create(&SendConnectThread, NULL, SendClientConnection, (void *)&t);
	if (rc)
	{
		cout<<"problem in creating thread"<<endl;
	}
	
	while(!connect_complete);
	cout<<"Connection completed"<<endl;	
	
}


int askServerFile(list <SocketWrapper> *SocketConnectionListServer,list <string> *files, int client_num)
{
	int random_server=rand()%3;

	char req_msg[100]={0};
	
	//snprintf( req_msg, sizeof(req_msg), "%s:%d:%d", "ENQUIRE", data->client_num);
	//send(data->socket, &req_msg , strlen(req_msg) , 0 );
	//list <char*> files;
	list <SocketWrapper> :: iterator it1;
		
	for(it1 = SocketConnectionListServer->begin(); it1 != SocketConnectionListServer->end(); ++it1) 
	{
		//cout<<(*it1).connect_num<<endl;
		if((*it1).connect_num==random_server)
		{
			//cout<<"req sent to server"<<endl;
			snprintf( req_msg, sizeof(req_msg), "%s:%d:%d", "ENQUIRE",client_num,random_server);
			send(((*it1).socket_id).socket_fd,&req_msg , strlen(req_msg) , 0);
			char buf1[100]={0};
			//sleep(1);
			int valread = read(((*it1).socket_id).socket_fd, buf1, 100); 
			string buffer(buf1);
			cout<<"From server "<<buf1<<endl;
			
			std::string delimiter = ",";

			size_t pos = 0;
			std::string token;
			while ((pos = buffer.find(",")) != std::string::npos) 
			{
			    token = buffer.substr(0, pos);
			    files->push_back(token);
			    buffer.erase(0, pos + delimiter.length());
			}
			break;
		}
	}
	
}


struct Request_thread_data
{
	int client_num;
	//list <SocketWrapper> *SocketConnectionList;
	//list <SocketWrapper> *SocketConnectionListServer;
	int *Seq_num;
	int socket;
	//int *Reply_expected;
	int resource_num;

};
struct Reply_thread_data_array
{
	struct Reply_thread_data *Reply;
};
struct Reply_thread_data
{
	int client_num;
	int Seq_num=0;
	int Highest_Seq_num;
	bool Reply_Deferred[5];
	bool Using;
	bool Waiting;
	bool A[5];
	int socket[5];

};
void *REQUEST_CS(void *threadarg)
{
	struct Request_thread_data *data;
	data = (struct Request_thread_data *) threadarg;
	
	char req_msg[100]={0};
	
	snprintf( req_msg, sizeof(req_msg), "%s:%d:%d:%d", "REQ", data->client_num,*data->Seq_num,data->resource_num);

	send(data->socket, &req_msg , strlen(req_msg) , 0 );
	
}
void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data_array *data;
	data = (struct Reply_thread_data_array *) threadarg;
	int resource_num;
	int Client_Req_num;// j
	int Client_Rep_num;
	int Req_Seq_num;//k
	char* reply_msg;
	char* temp;
	int temp_thread_id;
	pthread_mutex_lock(&Id_thread_lock);
	temp_thread_id=Id_thread;
	if(temp_thread_id==data->Reply->client_num)
	{
		Id_thread++;
		temp_thread_id=Id_thread;
	}
	Id_thread++;
	pthread_mutex_unlock(&Id_thread_lock);
	
	
	bool Our_Priority=FALSE;
	

	while(1)
	{	
		
		char buf[100]={0};
		
		int valread = read(data->Reply->socket[temp_thread_id], buf, 100); 
		string buffer(buf);
		
		size_t found = buffer.find("REPLY");
  		size_t found1 = buffer.find("REQ");
		
		if(valread && found!=string::npos)
		{
			std::string delimiter = ":";
			
			size_t pos = 0;
			std::string token;
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length());
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length());
			Client_Rep_num=atoi(token.c_str());	
			
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			
			resource_num=atoi(token.c_str());
			
			pthread_mutex_lock(&struct_lock);
			(data->Reply)[resource_num].A[Client_Rep_num]=TRUE;
			pthread_mutex_unlock(&struct_lock);
			
			valread=0;
		}
		else if (valread && found1!=string::npos)
		{
			std::string delimiter = ":";
			
			size_t pos = 0;
			std::string token;
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length());


			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length()); 
	    		Client_Req_num=atoi(token.c_str()); 


			
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length());
			Req_Seq_num=atoi(token.c_str());
			


			token = buffer.substr(0, buffer.find(":"));
			
			resource_num=atoi(token.c_str());
			
			pthread_mutex_lock(&struct_lock);
			(data->Reply)[resource_num].Highest_Seq_num=max((data->Reply)[resource_num].Highest_Seq_num,Req_Seq_num);
			pthread_mutex_unlock(&struct_lock);
			
			if( (Req_Seq_num > (data->Reply)[resource_num].Seq_num)  ||  ( (Req_Seq_num == (data->Reply)[resource_num].Seq_num) && (Client_Req_num > (data->Reply)[resource_num].client_num) ))
			{
				
				Our_Priority=TRUE;
				
			}
			
			if((data->Reply)[resource_num].Using ||((data->Reply)[resource_num].Waiting && Our_Priority))
			{
				pthread_mutex_lock(&struct_lock);
				(data->Reply)[resource_num].Reply_Deferred[Client_Req_num]=TRUE;
				pthread_mutex_unlock(&struct_lock);	
				cout<<"Defered reply to "<<Client_Req_num<<endl;
			}
			
			if(  !((data->Reply)[resource_num].Using || (data->Reply)[resource_num].Waiting) || ((data->Reply)[resource_num].Waiting && !((data->Reply)[resource_num].A[Client_Req_num]) && !(Our_Priority) ))
			{
				pthread_mutex_lock(&struct_lock);
				(data->Reply)[resource_num].A[Client_Req_num]=FALSE;
				pthread_mutex_unlock(&struct_lock);
				char reply_msg[100]={0};	
				snprintf( reply_msg, sizeof(reply_msg), "%s:%d:%d", "REPLY",(data->Reply)[resource_num].client_num,resource_num);
				cout<<"Sent Reply to "<<Client_Req_num<<endl;
				send((data->Reply)[resource_num].socket[temp_thread_id], &reply_msg , strlen(reply_msg) , 0 );
			}
			
			if((data->Reply)[resource_num].Waiting && (data->Reply)[resource_num].A[Client_Req_num] && !Our_Priority)
			{
				pthread_mutex_lock(&struct_lock);
				(data->Reply)[resource_num].A[Client_Req_num]=FALSE;
				pthread_mutex_unlock(&struct_lock);
				char reply_msg[100]={0};	
				snprintf( reply_msg, sizeof(reply_msg), "%s:%d:%d", "REPLY",(data->Reply)[resource_num].client_num,resource_num);
				cout<<"Sent Reply to "<<Client_Req_num<<endl;
				send(data->Reply->socket[temp_thread_id], &reply_msg , strlen(reply_msg) , 0 );

				char req_msg[100]={0};
				snprintf( req_msg, sizeof(req_msg), "%s:%d:%d:%d", "REQ", (data->Reply)[resource_num].client_num,(data->Reply)[resource_num].Seq_num,resource_num);
				send((data->Reply)[resource_num].socket[temp_thread_id], &req_msg , strlen(req_msg) , 0 );
			}
			
		}
		
	
	}

}
int ricart_agrawala_algo(int N,int client_num,list <SocketWrapper> *SocketConnectionList,list <SocketWrapper> *SocketConnectionListServer,list <string> *ServerFiles)
{
	
	
	char reply[100]={0};
	char ServerReq[100]={0};	
	///thread
	pthread_t REPLY[4];
	struct Reply_thread_data *Rep=(struct Reply_thread_data *)malloc(sizeof(struct Reply_thread_data)*ServerFiles->size());
	
	for(int f=0;f<ServerFiles->size();f++)
	{
		Rep[f].Seq_num=5;
		Rep[f].Highest_Seq_num=0;
		
		Rep[f].client_num=client_num;
		Rep[f].Using=FALSE;
		Rep[f].Waiting=FALSE;
		for(int n=0;n<5;n++)
		{
			Rep[f].A[n]={FALSE};
			Rep[f].Reply_Deferred[n]={0};
		}
	
		int l;
		list <SocketWrapper> :: iterator itt;
		for(l=0, itt = SocketConnectionList->begin(); itt != SocketConnectionList->end(); ++itt,l++) 
		{		
		
			if((*itt).connect_num>client_num)
			{
				Rep[f].socket[(*itt).connect_num]=((*itt).socket_id).socket_fd;
			}
			else
			{
				Rep[f].socket[(*itt).connect_num]=((*itt).socket_id).return_accept_socket();
			}
			
		}
	}
	struct Reply_thread_data_array RepPointer;
	RepPointer.Reply=Rep;
	for(int r=0;r<5;r++)
	{
		if (r==client_num)
			continue;
		int rc = pthread_create(&REPLY[r], NULL, REPLY_CS, (void *)&RepPointer);
		if (rc)
		{
			cout<<"Problem with the creating Reply Thread.."<<endl;
			return 0;	
		}
	}
	pthread_t REQ[5];
	struct Request_thread_data Req[5];
	int s;
	list <SocketWrapper> :: iterator it;


	for(int count=0;count<20;count++)
	{
		int sleep_time=rand()%10;
		cout<<"My Sleep time is "<<sleep_time<<" sec "<<endl;
		usleep(sleep_time*1000000);
		int resource_file=rand()%ServerFiles->size();
		
		int R_W=rand()%2;
		list <string> :: iterator file;
		int k;
		string resource;
		for(k=0, file = ServerFiles->begin(); file != ServerFiles->end(); ++file,k++) 
		{
			if(k==resource_file)
			{
				resource=*file;
				break;
			}
    			
		}
		if(R_W==1)
			cout<<"Requesting to write file "<<resource<<endl;
		else
			cout<<"Requesting to read file "<<resource<<endl;
		cout<<"Request entry to the Critical Section"<<endl;
		pthread_mutex_lock(&struct_lock);
		Rep[resource_file].Waiting=TRUE;
		Rep[resource_file].Seq_num=Rep[resource_file].Highest_Seq_num+1;
		pthread_mutex_unlock(&struct_lock);
		for(s=0, it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it,s++) 
		{
			
			Req[s].client_num=client_num;
			Req[s].Seq_num=&Rep[resource_file].Seq_num;
			Req[s].resource_num=resource_file;
			if((*it).connect_num>client_num)
			{
				Req[s].socket=((*it).socket_id).socket_fd;
			}
			else
			{
				Req[s].socket=((*it).socket_id).return_accept_socket();
			}
			
			int rc = pthread_create(&REQ[s], NULL, REQUEST_CS, (void *)&Req[s]);
			if (rc)
			{
				cout<<"Problem with the creating Request Thread.."<<endl;
				return 0;	
			}

		}
		for(int i=0;i<N;i++)   //optimization waiting
		{	
			if(i==client_num)
				continue;
			while(!Rep[resource_file].A[i]);
						
		}
		pthread_mutex_lock(&struct_lock);
		Rep[resource_file].Waiting=FALSE;
		Rep[resource_file].Using=TRUE;
		pthread_mutex_unlock(&struct_lock);
		
		cout<<"PERFORM CRITICAL SECTION"<<endl;
		
		int server_no=rand() % 3;//contact a random server to write to file
		
		
		if (R_W==1)
		{	
			cout<<"Writing to all Servers"<<endl;
			list <SocketWrapper> :: iterator it1;
			for(it1 = SocketConnectionListServer->begin(); it1 != SocketConnectionListServer->end(); ++it1) 
			{
			
				snprintf( ServerReq, sizeof(ServerReq), "%s:%s:%d:IP Address - %s with Seq_num - %d", "WRITE",resource.c_str(),(*it1).connect_num,getIPAddress().c_str(),Rep[resource_file].Seq_num);
				send(((*it1).socket_id).socket_fd,&ServerReq , strlen(ServerReq) , 0);
				char buf1[100]={0};
				//sleep(1);
				int valread = read(((*it1).socket_id).socket_fd, buf1, 100); 
				string buffer1(buf1);
				cout<<"Message from Server - "<<buf1<<endl;
				
					
				
			}
		}
		else
		{
			cout<<"Connecting to Random Server "<<server_no<<endl;
			list <SocketWrapper> :: iterator it1;
			for(it1 = SocketConnectionListServer->begin(); it1 != SocketConnectionListServer->end(); ++it1) 
			{
				
				if((*it1).connect_num==server_no)
				{
					//cout<<"req sent to server"<<endl;
					snprintf( ServerReq, sizeof(ServerReq), "%s:%s:%d:%d", "READ",resource.c_str(),server_no,client_num);
					send(((*it1).socket_id).socket_fd,&ServerReq , strlen(ServerReq) , 0);
					char buf1[100]={0};
					
					int valread = read(((*it1).socket_id).socket_fd, buf1, 100); 
					string buffer1(buf1);
					cout<<"Last Line From Server :"<<buf1<<endl;
					
				}
			}
		}
		
		Rep[resource_file].Using=FALSE;
		
		//Critical_Section=FALSE;
		for(int i=0;i<N;i++)
		{
			if(Rep[resource_file].Reply_Deferred[i]==TRUE)
			{
				pthread_mutex_lock(&struct_lock);
				Rep[resource_file].A[i]=FALSE;
				Rep[resource_file].Reply_Deferred[i]=FALSE;
				pthread_mutex_lock(&struct_lock);
				for(it = SocketConnectionList->begin(); it != SocketConnectionList->end(); ++it) 
				{
					if((*it).connect_num==i)
					{
						snprintf( reply, sizeof(reply), "%s:%d:%d", "REPLY",client_num,resource_file);
						if(i>client_num)
						{
							send(((*it).socket_id).socket_fd,&reply , strlen(reply) , 0);
						}
						else
						{
							send(((*it).socket_id).return_accept_socket(),&reply , strlen(reply) , 0);
						}
						cout<<"Sent Deferred Reply to "<<i<<endl;
					}
				}
			}
		}
	

	}
cout<<"waiting state"<<endl;
while(1){}
}

int main()
{	
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num;
	srand (time(NULL));
	client_num=findClientNum(getIPAddress());
	cout<<client_num;
	//pthread_t ClientThread;
	list <SocketWrapper> SocketConnectionList;
	list <SocketWrapper> SocketConnectionListServer;
	list <string> ServerFiles;
	int status=makeConnection(&SocketConnectionList,&SocketConnectionListServer);
	cout<<"*****************Connection Established******************"<<endl;
	int res=askServerFile(&SocketConnectionListServer,&ServerFiles,client_num);
	
	int stat=ricart_agrawala_algo(N,client_num,&SocketConnectionList,&SocketConnectionListServer,&ServerFiles);
	cout<<"**DONE**"<<endl;
	while(1)
	{
	}
	
		
	return 0;
}

////server
#include <ifaddrs.h>
#include "connection.h"
#include <thread>
using namespace std;
#define PORT  2408
#define TRUE             1
#define FALSE            0
pthread_mutex_t list_lock;
pthread_mutex_t resource_lock;



string getIPAddress(){
    string ipAddress="Unable to get IP Address";
    struct ifaddrs *interfaces = NULL;
    struct ifaddrs *temp_addr = NULL;
    int success = 0;
    // retrieve the current interfaces - returns 0 on success
    success = getifaddrs(&interfaces);
    if (success == 0) {
        // Loop through linked list of interfaces
        temp_addr = interfaces;
        while(temp_addr != NULL) {
            if(temp_addr->ifa_addr->sa_family == AF_INET) {
                // Check if interface is en0 which is the wifi connection on the iPhone
                if(strcmp(temp_addr->ifa_name, "en0")){
                    ipAddress=inet_ntoa(((struct sockaddr_in*)temp_addr->ifa_addr)->sin_addr);
                }
            }
            temp_addr = temp_addr->ifa_next;
        }
    }
    // Free memory
    freeifaddrs(interfaces);
    return ipAddress;
}
class Socket_connection
{
public:
	int socket_fd, new_socket, valread; 
	struct sockaddr_in address; 
	int opt = 1; 
	int addrlen = sizeof(address); 
	//char buffer[1024] = {0}; 
	//char *hello = "Hello"; 
	int iMode=0;
	Socket_connection()
	{
		// Creating socket file descriptor 
		if ((socket_fd = socket(AF_INET, SOCK_STREAM, 0)) == 0) 
		{ 
			perror("socket failed"); 
			exit(EXIT_FAILURE); 
		} 
		bzero((char *) &address, sizeof(address));
		ioctl(socket_fd, FIONBIO, &iMode); 
		address.sin_port = htons( PORT );
		address.sin_family = AF_INET;
		 
	}
	Socket_connection(const Socket_connection &sock)  //copy constructor
	{
	socket_fd=sock.socket_fd;
	new_socket=sock.new_socket; 
	valread=sock.valread;
	address=sock.address; 
	opt=1;
	addrlen =sock.addrlen;
	iMode=0;

	}
	~Socket_connection()  //destructor
	{
	
	}
	int connect_socket(char* IPname)
	{	
		
		//address.sin_addr.s_addr = inet_addr(IPname);
		if(inet_pton(AF_INET, IPname, &address.sin_addr)<=0)  
    		{ 
        		cout<<"Invalid address/ Address not supported "<<endl; 
        		return 0; 
    		}		
		//int status;
		//cout<<addrsess.sin_addr.s_addr<<endl;
		if(connect(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{
			cout<<"Connection Failed "<<endl; 
        		return 0;
		}
		else
		{
			return 1;
		}
		
		
		
    		
	}
	int listen_socket()
	{	
		
		// Forcefully attaching socket to the port 2408 
		if (setsockopt(socket_fd, SOL_SOCKET, (SO_REUSEPORT | SO_REUSEADDR),&opt, sizeof(opt))) 
		{ 
		perror("setsockopt"); 
		exit(EXIT_FAILURE); 
		} 
		address.sin_addr.s_addr = INADDR_ANY; 
		
		// Forcefully attaching socket to the port 8080 
		if (bind(socket_fd, (struct sockaddr *)&address, sizeof(address))<0) 
		{ 
		perror("bind failed"); 
		return 0; 
		} 
		
		if (listen(socket_fd, 32) < 0) 
		{ 
		perror("listen failed"); 
		return 0; 
		} 
		/*
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		*/
	}
	int return_accept_response()
	{
		cout<<"waiting to connect here"<<endl;
		if ((new_socket = accept(socket_fd, (struct sockaddr *)&address,(socklen_t*)&addrlen))<0) 
		{ 
		perror("accept failed"); 
		return 0; 
		} 
		else
		{
			return 1;
		}
		
	}
	int return_accept_socket()
	{
		return new_socket;
	}
	
			
};
class SocketWrapper
{
public:
	string sender;
	string receiver;
	Socket_connection socket_id;//=new Socket_connection();
	int connect_num;
	SocketWrapper()
	{
		sender="";
		receiver="";

	}
	SocketWrapper(const SocketWrapper &wrap)//copy constructor
	{
		sender=wrap.sender;
		receiver=wrap.receiver;
		socket_id=wrap.socket_id;
		connect_num=wrap.connect_num;

	}
	~SocketWrapper()//destructor
	{
	}
	
};
string read_directory(char* name)
{
		
	char files[200];
	DIR* dirp = opendir(name);
	list<string> filesList;
	struct dirent * dp;
	
	while ((dp = readdir(dirp)) != NULL) 
	{
	
		if(strcmp(dp->d_name,".")==0 || strcmp(dp->d_name,"..")==0 || string(dp->d_name).back()=='~')
			continue;
		//strncat(files,dp->d_name,strlen(dp->d_name));
		filesList.push_back(dp->d_name);
		//strncat(files,",",strlen(","));
	}
	closedir(dirp);
	filesList.sort();
	list<string>::iterator it2;

	
	for (it2 = filesList.begin(); it2 != filesList.end(); it2++)
	{  	
		strcat(files,(*it2).c_str());
		strncat(files,",",sizeof(","));
	}
	return string(files);
}
int findClientNum(string IP)
{
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	//char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int N=5; //Total number of Clients
	int client_num=0;
	for(int i=0;i<N;i++)// find the client number
	{
		if(IP==string(clientIPList[i]))
		{
			client_num=i;
			break;	
		}
	}
	return client_num;
}
void writeFile(char* filename,string appendText)
{

  	std::ofstream outfile;

  	outfile.open(filename, std::ios_base::app);
  	outfile << appendText<<endl;;


}
string getLastLine(char* filename)
{
	std::string lastline;
	std::ifstream fs;
	fs.open(filename, std::fstream::in);
	if(fs.is_open())
	{
		//Got to the last character before EOF
		fs.seekg(-1, std::ios_base::end);
		if(fs.peek() == '\n')
		{
			//Start searching for \n occurrences
			fs.seekg(-1, std::ios_base::cur);
			int i = fs.tellg();
			for(i;i > 0; i--)
			{
				if(fs.peek() == '\n')
				{
 					 //Found
  					fs.get();
  					break;
				}		
				//Move one character back
				fs.seekg(i, std::ios_base::beg);
			}
		}

		getline(fs, lastline);
		std::cout << lastline << std::endl;
	}
	else
	{
		std::cout << "Could not find end line character" << std::endl;
		lastline="no content";
	}	
	return lastline;
}
int makeConnection(list <SocketWrapper> *SocketConnectionList)
{	
	cout<<"Main Client Thread created"<<endl;
	
	char* clientIPList[5]={CLIENT0,CLIENT1,CLIENT2,CLIENT3,CLIENT4};
	char* serverIPList[5]={SERVER0,SERVER1,SERVER2};
	int connection_start=0,status,client_num,flag=0,rc;
	char *setup_msg = "received";
	cout<<"My Ip address is::"<<getIPAddress()<<endl;
	
	Socket_connection s1;//=new Socket_connection();
	s1.listen_socket();
	while(!(connection_start==1))
	{	
		
		int stat=s1.return_accept_response();
		
		cout<<"listened.."<<endl;
		if (stat==1) 
		{	
			
						
			send(s1.return_accept_socket(), setup_msg , strlen(setup_msg) , 0 ); 

			SocketWrapper w1;//=new SocketWrapper();			//need mutex
			w1.sender=inet_ntoa(s1.address.sin_addr);
			w1.receiver=getIPAddress();
			w1.socket_id=s1;
			w1.connect_num=findClientNum(string(inet_ntoa(s1.address.sin_addr)));
			pthread_mutex_lock(&list_lock);
			SocketConnectionList->push_back(w1);
			pthread_mutex_unlock(&list_lock);
			cout<<"Receiver - connected from "<<inet_ntoa(s1.address.sin_addr) <<" to "<<getIPAddress()<<endl;
			//cout<<"connected"<<endl;
			if (SocketConnectionList->size()==5)
				connection_start=1;
									
		}
		else
		{	
			cout<<"couldnt connect to the socket-receiver side"<<endl;
		
		}
		
		
	}
		
	cout<<"Connection completed"<<endl;	
	
}


struct Reply_thread_data
{
	int socket;
	int client_num;
};

void *REPLY_CS(void *threadarg)
{
	struct Reply_thread_data *data;
	data = (struct Reply_thread_data *) threadarg;
	char finish_msg[100] ={0};
	char directory[200]={0};
	char filesList[100]={0};
	char* dir="/home/012/j/jx/jxb162030/Desktop/aos/";
	while(1)
	{
		//cout<<"waiting for any req msg from clients"<<endl;
		char buf[100]={0};
		
		int valread = read(data->socket, buf, 100); 
		string buffer(buf);
		
		size_t found0 = buffer.find("ENQUIRE");
		size_t found1 = buffer.find("WRITE");
		size_t found2 = buffer.find("READ");
		std::string delimiter = ":";
		
		pthread_mutex_lock(&resource_lock);
		if(valread && (found0 != string::npos))
		{	
			
			int client,server;
			size_t pos = 0;
			std::string token;
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length());


			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length()); 
	    		client=atoi(token.c_str()); 


			
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length());
			server=atoi(token.c_str());

			snprintf( directory, sizeof(directory), "%s%d", dir,server);
    			string ServerFiles=read_directory(directory);
			snprintf( filesList, sizeof(filesList), "%s", ServerFiles.c_str());
			send(data->socket, filesList , strlen(filesList) , 0 );

		}
		else if(valread && (found1 != string::npos))
		{
			string filename,writeString,server;
			size_t pos = 0;
			std::string token;
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length());


			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length()); 
	    		filename=token;

			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length()); 
	    		server=token;

			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length()); 
	    		writeString=token;
			
			char temp1[200]={0};
			snprintf( temp1, sizeof(temp1), "%s%s/%s", dir,server.c_str(), filename.c_str());
			cout<<temp1<<endl;
			writeFile(temp1,writeString.c_str());
			cout<<"written to file :"<<filename<<endl;
			snprintf( finish_msg, sizeof(finish_msg), "%s%s", "written succesfully on file ",filename.c_str());
			send(data->socket, &finish_msg , strlen(finish_msg) , 0 );
		}
		else if(valread && (found2 != string::npos))
		{
			
			string filename,client,server;
			size_t pos = 0;
			std::string token;
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length());

			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length()); 
	    		filename=token;
		
			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length()); 
	    		server=token;

			pos = buffer.find(":");
			token = buffer.substr(0, buffer.find(":"));
			buffer.erase(0, pos + delimiter.length()); 
	    		client=token;
			
			char temp1[200]={0};
			snprintf( temp1, sizeof(temp1), "%s%s/%s", dir,server.c_str(), filename.c_str());
			string lastLine=getLastLine(temp1);
			cout<<"Client "<<client<<" is reading the last data from "<<filename<<" : "<<lastLine<<endl;
			snprintf( finish_msg, sizeof(finish_msg), "%s", lastLine.c_str());
			send(data->socket, &finish_msg , strlen(finish_msg) , 0 );
		}
		pthread_mutex_unlock(&resource_lock);
	}

}
int ReplyClient(list <SocketWrapper> *SocketConnectionList)
{
	pthread_t REPLY[5];
	struct Reply_thread_data Rep[5];
	int l;
	list <SocketWrapper> :: iterator itt;
	for(l=0, itt = SocketConnectionList->begin(); itt != SocketConnectionList->end(); ++itt,l++) 
	{
		Rep[l].socket=((*itt).socket_id).return_accept_socket();
		Rep[l].client_num=(*itt).connect_num;
		int rc = pthread_create(&REPLY[l], NULL, REPLY_CS, (void *)&Rep[l]);
		if (rc)
		{
			cout<<"Problem with the creating Reply Thread.."<<endl;
			return 0;	
		}
	}
	

}
int main()
{	
	
	list <SocketWrapper> SocketConnectionList;
	
	int status=makeConnection(&SocketConnectionList);
	int stat=ReplyClient(&SocketConnectionList);
	while(1)
	{
	}
	
		
	return 0;
}
