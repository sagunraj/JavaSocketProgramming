# JavaSocketProgramming

## Socket Programming (Client Server Computing)

### Socket:
- Combination of IP addresss and port number.
- A socket identifies an endpoint of a two way communication link between two programs running on the network.
- When a client requests for a connection on a particular port, the server identifies and keeps track of the socket that it will use to communicate with that client.
- A server can communicate on the same port with many clients using sockets to determine the destination and source of the communication.

#### TCP Connection using Socket:
1. ServerSocket():
- The ServerSocket class is used to listen for incoming connections that is to wait for the clients to connect.
- The constructors defined in class ServerSocket are listed below:
	ServerSocket() constructor -> creates an unbound server socket.
	ServerSocket(int portnumber) -> creates a server socket and binds it to the port number specified by port number.
	ServerSocket(int portnumber, int qlength) -> creates a server socket, binds it to the specified port number and creates a queue of length specified by qlength for that port. The queue length specifies the number of client requests that can be kept in waiting list.

2. Socket class:
- Socket() constructor -> creates an unconnected socket.
- Socket(InetAddress addresss, int portnumber) -> creates a socket and connects it to the specified port number at the specified IP address.
- Socket(String hast, int portnumber) -> creates a socket and connects it to the specified port number on the named host.

**Steps for creating a TCP Server:**
1. Open the server socket.
2. Wait for the client request.
3. Create I/O streams for communicating to the client.
	- InputStream and OutputStream
		- getInputStream -getOutputStream
4. Communicate with the client.
5. Close the socket.

**Steps for creating a TCP Client:**
1. Create a socket object.
2. Create I/O streams for communicating with the server.
3. Communicate with the server.
4. Close the socket when the communication ends.

**TCP Server Program**
```import java.net.*;  
import java.io.*;  
public class ServerExample {
	public static void main(String[] args) {
		int port = 2316;
		try {
			// creates a socket on port 2316
			ServerSocket s1 = new ServerSocket(port);
			// waits and accepts a connection
			Socket socket = s1.accept();
			// associates streams with the socket
			OutputStream sout = socket.getOutputStream();
			DataOutputStream out = new DataOutputStream(sout);
			// sends output to the standard output device
			System.out.println("Hello! This is socket programming.");
			// sends string to the client
			out.writeUTF("Server is ready.");
			//sends output to the standard output device
			System.out.println("Waiting for the next line...");
			// closes the connection except server socket
			out.close();
			sout.close();
			socket.close();
		}
		catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```
**TCP Client Program:**
```import java.net.*;
import java.io.*;
public class ClientExample {
	public static void main(String[] args) {
		int port = 2316;
		try {
			InetAddress ipAddress = InetAddress.getByName("localhost");
			System.out.println("Address and the port number is:" + ipAddress + "and port " + port + "?");
			// opens connection to the server at port 2316
			Socket socket = new Socket(ipAddress, port);
			System.out.println("I am the client.");
			// obtains an input file handle from socket
			InputStream sin = socket.getInputStream();
			DataInputStream in = new DataInputStream(sin);
			String pstr = new String(in.readUTF());
			System.out.println(pstr);
			// closes connection
			sin.close();
			in.close();
			socket.close();
		}
		catch (Exception e) {
			e.printStackTrace();
		}
	}
}			
```
**TCP Client Server Chat Program**
*Client.java*
```
import java.net.*;
import java.io.*;
public class Client{
	static final int port=1011;
	public static void main(String[] args) throws Exception{
		Socket s=new Socket("localhost",port);
		DataInputStream ds=new DataInputStream(s.getInputStream());
		DataOutputStream os=new DataOutputStream(s.getOutputStream());
		String clientmessage="";
		String servermessage="";
		BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
		while(!clientmessage.equals("stop")) {
			clientmessage=br.readLine();
			os.writeUTF(clientmessage);
			os.flush();
			servermessage=ds.readUTF();
			System.out.println("From Server:"+servermessage);
			
		}
		os.close();
		s.close();
	}
}
```

*Server.java*
```
import java.io.*;
import java.net.*;
public class Server{
	static final int port=1011;
	public static void main(String[] args) throws Exception{
		ServerSocket ss=new ServerSocket(port);
		Socket s=ss.accept();
		DataInputStream is=new DataInputStream(s.getInputStream());
		DataOutputStream os=new DataOutputStream(s.getOutputStream());
		String clientmessage="";
		String servermessage="";
		BufferedReader br=new BufferedReader(new InputStreamReader(System.in));
		while(!clientmessage.equals("stop")) {
			clientmessage=is.readUTF();
			System.out.println("From Client:"+clientmessage);
			servermessage=br.readLine();
			os.writeUTF(servermessage);
			os.flush();
		}
		os.close();
		s.close();
		ss.close();
		
	}
}```

#### UDP Socket Programming:

**DatagramSocket**
This class is used to send or recieve the datagram packets.
The constructors along with their descriptions are:
- DatagramSocket() -> Creates a datagram socket.
- DatagramSocket(int portnumber, InetAddress ipAdress) -> Creates a datagram socket and binds in the port and the InetAddress specified portno and ipAddress respectively.
- DatagramSocket(socketAddress address) -> Creates a diagram socket and binds it to the SocketAddress speccified by the address.

The methods along with their description are:
- void send(DatagramPacket packet) -> sends packet to the ports.
- void receive(DatagramPacket packet) -> Receives packet specified by packet fro the port.

**DatagramPacket**
The constructors along with their descriptions are:
- DatagramPacket(byte data[], int length) -> creates a DatagramPacket for receiving the packets of length in the buffer.
- DatagramPacket(byte data[], int length, InetAddress ipAddress, int portnumber) -> creates a DatagramPacket for sending packets of length to the specified port number on the specified host.

**UDPServer.java**  
```import java.net.*;  
import java.io.*;  
import java.util.*;  
class UDPServer {  
	public static void main(String args[]) throws Exception {  
		int serverportno = 1139;  
		int clientportno = 1140;  
		try {  
			DatagramSocket ds = new DatagramSocket(serverportno);  
			//obtains the IP address for localhost  
			InetAddress ipadd = InetAddress.getLocalHost();  
			String pstr = "Hello, this is your message.";  
			byte buf[] = pstr.getBytes();  
			//creates a Datagram packet for sending the packets  
			DatagramPacket sendPacket = new DatagramPacket(buf, buf.length, ipadd, clientportno);  
			ds.send(sendPacket);  
			ds.close();  
		}  
		catch(Exception e) {  
			System.out.println(e.getMessage());  
		}  
	}   
}
```
**UDPClient.java**  
```import java.net.*;  
import java.io.*;  
class UDPClient {  
	public static void main(String[] args) throws Exception {  
			int serverport = 1139;  
			int clientport = 1140;  
			try {  
				DatagramSocket ds = new DatagramSocket(clientportno);  
				byte b[] = new byte[1024];  
				DatagramPacket dp = new DatagramPacket(b, b.length);  
				ds.receive(dp);  
				System.out.println(new String(dp.getData());  
			}  
			catch(Exception e) {  
				System.out.println(e.getMessage());  
			}
		}
	}
```

#### Remote Method Invocation (RMI)
![Image of RMI](https://github.com/sagunraj/JavaSocketProgramming/blob/master/RMI.jpg)
**RMI Steps:**  
1. Create the remote interface
2. Implementation of remote interface
3. Compile, stub and skeleton (rmic)
4. Start the registry (rmi registry)
5. Create and start server
6. Create and start client
