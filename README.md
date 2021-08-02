
**DO NOT LOSE YOUR TIME**

Anna Ramon i Adrià Busquets 

31/5/2021




**Summary:** Bot.py is a program which shows the shortest path between two points given by the user. The user sends a departure location and an arrival location, then the user recives an image of a map with the fastest rout between this two points painted on it. Bot.py uses the functions of igo.py to reach its goal.


## **LAUNCHING THE PROGRAM**
***

This program works with the Telegram app and the programs bot.py and igo.py.

### **Prerequisites**
It's essential the use of an electronical device where the user can download the Telegram app and the programs bot.py and igo.py. If you want to use the functions of the bot.py, you must have a _Telegram bot_. The bot.py program requires a TOKEN access which has to be kept in the same working directory where the igo.py and bot.py programs were downloaded. Here you have a brief tutorial explaining how to create a Telegram bot: <https://xn--llions-yua.jutge.org/python/telegram.html>.
Once the program is executed, everyone can start a chat with your bot.


`Libraries`
The program uses some functions from different libraries, if the user wants to use this program properly, it's crucial to download the following libraries: 
- staticmap 
- network 
- osmnx 
- csv 
- pickle 
- urllib 
- os.path 

For downloading the libraries, it's enough typing in the shell the following line, depending on the user's operative sistem.

```
Mac OS: pip3 install library_name
Ubuntu OS: sudo apt -get install library_name
```


### **Getting ready** 
Once the user has all the prerequisites installed, the bot.py program is ready to be executed from the shell. It's important to be in the same directory where the programs had been downloaded and then execute the command _python3 bot.py_.

Now, the user can look for the bot in Telegram and start using it sending the **_start_** message. It's very important to send this message, otherwise, the program won't work.



## **EXECUTING THE BOT**
***
At this point, the bot.py program has been executed in the shell and the user has found his bot in the Telegram app. From now on, we wil never get lost! The bot goal is to guide us from a point to another. To start a conversation with the bot and finding out their functions just send him the message */help*. The bot will answer you sending the functions that he can execute, how to execute them and a brief explanation of each one.


<img src="https://user-images.githubusercontent.com/88190336/127868001-218acbbf-f53f-4658-8556-c3f3a932445b.png" width=200 >


_**AnnaRamonHinojosa** is an example of one Telegram bot_



## **LET'S UNDERSTAND HOW IT WORKS**
***
In this section you will understand how work each program independently and its functions. We have designed this program for calculating the shortest rout between two points in Barcelona.

### Igo.py
The module igo.py contains the code related with getting, keeping and consulting information of the Barcelona street's graphs, of its trams and the traffic in each one. This module is able to calculate the shortest path between two points taking into account the length of Barcelona's streets as well as their maximum speed and traffic congestion in each moment. A part of calculating the shortest path between two points, igo.py shows an image of a map with the shortest path calculated, painted on it. 
For downloading the graph and using it we use this private functions, which the name explains exactly what they do: 
- _get_graph_ 
- _exists_graph_ 
- _download_graph_ 
- _save_graph_ 
- _load_graph_ 

For finding the shortest path taking into account the items explained before, we create a new concept _itime_. 


`ITIME`

In few words, the itime of a tram is:
```
itime = time * congestion_tram
```
time = legth of the tram divided by the maximum speed of the tram 

Itime is an attribute that we decided to create and add to each edge of the Barcelona street's graph for allowing the calculation of the shortest path between two points (function **_calculating_itime_**). It considers, the maximum speed and length (those are attributes given by the original graph) of each edge (trams) and the congestion (given by the congestion url) of each tram. The highways and congestions url have information of few trams of the original graph, so if we have congestion information we use it (this information is downloaded in the functions **_download_highways_** and **_download_congestions_**, and kept each one into a different dictionary, both dictionaries can be related by its key - way id). 
When we don't have information of a tram, we assign an hypothetical congestion based on the type of tram that it is. All the edges in the graph have an attribute named _highway_ which says the tram type. In the function **_calcular_congestio_** you can observe the procedure followed: a primary tram is considered to be _molt fluid_ because it has a fast maximum speed and more than one lane, a residential street is considered to be _dens_. So we assign a congestion depending on the tram's type. Another possibility is that the maximum speed of the tram is undetermined, then we assign an hypothetical one following the same procedure as in congestions (function **_calcular_speed_**).
The lower the itime is, the fastest is the tram. 

![attributes](https://user-images.githubusercontent.com/88190336/127867846-b263263d-5154-4a9b-bad8-12a52b664836.png) 

yellow: highway type - green: length - blue: maxspeed. Those are ones of the original graph attributes that the program uses for calculating the itime. It's an example that in some trams there's no maxspeed attribute.


`ALGORITHM STRATEGY` 
Download the Barcelona street's graph with a generic itime added in each tram, generic itime means the resulting itime of following the procedure explained before for the trams with no information. So, we add a hypothetical itime in all the trams, even in those with information. The first time we use the function **_get_graph_** it executes the function **_completar_graph_** which downloads the graph with the generic itime added. The following times we us the **_get_graph_** function we will only have to **_load_graph_**, we keep this digraph. Then in the **_built_igraph_** function the itime of the highways with information is modified for the correct one. This is the strength of the algorithm and makes the program faster. The congestion information changes every 5 minutes, which means the itime of the trams with information have to change every 5 minutes but the hypothetical itimes of the trams without information are the same all the time and can save time just calculating once and keeping it. 

In the function **_get_igraph_** we get the graph with the attribute itime updated in all the edges. If we use this function for the first time or 5 minutes later than the last time we use it, the congestion information has changed, so it's necessary to update the itime attribute with the **_built_igraph_** function. Otherwise, if we use the function before 5 minutes have elapsed since the last execution, we use the same graph, because the itime hasn't changed yet. Thanks to this, our program is very efficient and just lasts 0.5 seconds finding a new rout asked for.

We know that the possibility of changing the speed of a tram without congestion information exists and would modify its itime, nevertheless, the change of speed would be minimum, so the itime would change insignificantly compared to the time efficiency that we win by downloading the graph once and just updating the itimes with congestion information.

The plot functions are helpers which allows making some experiments and proving if the highways download properly, or the congestions... The plots shows an image of the highways ( **_plot_hihghways_** ), of the congestions painted on a map image with different colors depending on the congestion level ( **_plot_congestions_** ), of a given path ( **_plot_path_** ).  
Finally, the main function is **_shortest_path_** which calculates the shortest path between two points taking into account the **itime** attribute. **_star_system_** function downloads the graph if it has not been saved yet. 

<img src="https://user-images.githubusercontent.com/88190336/127869121-d8925b9c-f613-4077-9b90-2dc3a86ae3e0.png" width=300 >

**_plot_congestions_** output


### Bot.py
The module bot waits for different users connections and helps them reaching their destinations by sending them the fastest rout for going from their current location, which can be real or fake, to their destination.
The bot have some functions accessible for the users, those are able to consult just sending /help (function **_help_** ). When the users start the bot (function **_start_**) the program downloads the Barcelona street's graph if it hasn't been downloaded yet. From now on, the user can ask for the shortest rout from his current location to anywhere from Barcelona.  
The function **_go_** is the one which calculates the shortest path between two points. This function has a timer, the key of the program efficiency. Every 5 minutes the congestion information of the Barcelona's trams is updated, so the itime of this trams change. The itime of the trams without information don't change, because it's hypothetical. So, as it's explained in the igo.py section, we firstly download a generic graph and we keep it. Then, every 5 minutes we update the itime attribute of the trams with congestion information, and we keep this new graph. During this 5 minutes, the program lasts less than 0.5 seconds finding a rout! 

The user can send a destination location sending the place coordinates or just the name of the place. The function **_get_coords_from_message_** translates both types of sending a location to latitude and longitude point coordinates for being able to use the functions in the igo.py program.

The function **_pos_** allows the user fakes his current location, and, on the other hand, the **_unpos_** function reverses this process, it stops using a user fake location for staring using the real one.

If the users wants to know his location, the function **_where_** sends an image of the last given location. 


- `start and where`
<center>
<img src="https://user-images.githubusercontent.com/88190336/127869630-aa1a2f75-b4b1-4efc-a9f0-65795565aa4c.PNG" width=200 >
<center>

- `pos and go` 

<center>
<img src="https://user-images.githubusercontent.com/88190336/127870013-2682dc88-cdd3-4b02-9cfa-2e8b9d40bf9d.PNG" width=200 > 
<center>
<center>

- `unpos` 

<center>
<img src="https://user-images.githubusercontent.com/88190336/127870500-b7405f36-47fe-4dbb-aa6c-f2be3bc644d3.PNG" width=200 > 
<center>
Stop using a fake location, use the user's current location.
<center>


## **USING THE BOT**
***
The bot is now started and is trying to connect. Once your bot is connected, you can query it for more information. It's important to send each message : 
```help``` it will show the functions than it can execute. 
As explained before, _go_ calculates the shortest path between two points. Firstly, the user has to send his location or fake one: 
```pos``` and the name of the fake location or the coordinates. 
Then executing: 
```go``` and the name or coordinates of the destination point. 
The user will recieve a picture of the shortest rout.


## **DESIGN DESITIONS**
***
As it has been explained along the readme file, this program has some programmers decisions: 

**Trams without congestion information**: an hypothetical congestion is assigned depending on the tram's type. 

**Trams with an undetermined speed**: an hypothetical one is  assigned following a criteria based on the street type. 

**Blocked trams**: an infinite weight edge is assigned. It exists the possibility that the shortest path between two points is the infinite one. In the real life we won't be able to go from a point to another one which have an infinite weight edge as a shortest path, this will mean that there's is no path matching this points. However, this situation wouldn't be solved properly by the program, the possibility of this situation is very rare, so we decided it is better to put an infinite weight in the edge than other possibility. 

**Update the itime** of the trams with information every 5 minutes and keep the itime of the ones generated by convenience. The first time we built the graph is _slowly_, it has to download the graph, the highways, the congestions, calculate a generic itime for each edge and change the itime of the trams with information (around 25 seconds). Nevertheless, this graph will be the same for the following 5 minutes, so calculating the shortest path in this 5 minutes will be very quick (around 0.5 seconds). The next 5 minutes the itime of the trams with information has to be updated, but the trams without congestions' information will be the same all the time, so once calculated we don't have to calculated again. 

Love the limitations, and craziness, of this project..
