Secure REST API
---------------
How to run : 
1. Go into the project directory.

2. Run the Server: 
   $ cd server 
   $ sbt compile 
   $ sbt run 

3. Run the Client: 
   $ cd client 
   $ sbt compile 
   $ sbt “run n”

Note: Here the ‘n’ refers to the number of users (clients) to simulate e.g. $ sbt “run 10”. The maximum number of users that we could simulate is 10,000 

Working :
The client program spawns ‘n’ number of client actors (users), and for every user, the client program initializes and maps some default information such as profile, page, friend list, user, picture and album. This information is AES-128 encrypted using the random key (generated using secure random number generator). 
Every few seconds the client actors make a post request (profile, page, friend list, post, picture, and album). After the post has been successfully made, all the friends (actors) of the client actor are notified, so that these friends can make an appropriate request (get) to see the posted data. 
Every few seconds, client actors also make get requests (profile, page, friend list, post, picture, and album).

Scenario 1:
Client actor “posts” some data to the server. 
For e.g.: Client-1 makes a “POST UserPost” request to the server. Below is the encryption scheme that is followed: 
1. First Client-1 encrypts the UserPost data with a personal “AES Key” and “AES Init Vector” that are not share with anyone (end-to-end encryption).  
2. Then, Client-1 decrypts the UserPost response (UserPost data returned back from server) using its personal “AES Key” and “AES Init Vector”.  
3. Then, Client-1 encrypts this UserPost again now with a random “AES Key” and “AES Init Vector”.  
4. The Client-1 then fetches all its friends and encrypts this random “AES key” and “AES Init Vector” with their respective “RSA public-keys”, and sends the Encrypted-UserPost, Encrypted-AES-key and Encrypted-AES-IV to all its friends.  
5. Now, all the friend actors first decrypt the encrypted AES key and AES Init vector using their respective “RSA private-keys”.  
6. Finally, using this decrypted “AES key” and “AES Init Vector” the friend actors decrypt the encrypted UserPost.  

Scenario 2:
Client actor “gets” some data from the server. 
For e.g. Client-1 actor makes a “GET Profile” request of Client-2. Below is the encryption scheme that is followed: 
1. First Client-1 actor notifies Client-2 actor that it needs Client-2’s profile data.  
2. Then, Client-2 actor fetches the encrypted profile data from server and decrypts it using  its personal “AES key” and “AES Init Vector”.  
3. The Client-2 then encrypts this Profile again using a random “AES Key” and “AES Init  Vector”.  
4. The Client-2 encrypts the random “AES key” and “AES Init Vector” using the “RSA public-  key” of Client-1, and sends the Encrypted-Profile, Encrypted-AES-key and Encrypted-  AES-IV to the Client-1.  
5. Now, the Client-1 actor first decrypts the encrypted “AES key” and “AES Init Vector”  using its “RSA private-key”.  
6. Finally, using this decrypted “AES key” and “AES Init Vector”, the Client-2 decrypts the  encrypted Profile data.  

The simulation strategy is decided based on the real statistics from the references mentioned at the end of this report. 
E.g., if the total number of Facebook users (clients) are 1000 then, 

To summarize, after populating all the users’ memory, the following simulation is achieved by random activity by client actors:
1. FRIENDLIST: Friend List is requested (Get) by random user for every 15 seconds and a Post friend request for every 15 seconds.  
2. POST: Get, Post requests for POST for every 5 seconds.  
3. PAGE: Get Page Posts request for every 10 seconds and Post requests for a PAGE for every 5 seconds.  
4. PICTURE: Get pictures for every 10 seconds and Post pictures for every 7 seconds.  
5. ALBUM: Get album requests for 10 seconds and Post album requests for every 6 seconds  
6. PROFILE: Get profile requests for every 10 seconds and Put profile requests for every 7 seconds.  

