---
title: "Nahamcon CTF 2023"
date: 2023-06-17T12:25:43-07:00
draft: false
---

## Nahamcon CTF 2023

![[Pasted image 20230617122718.png]]

## Read the rules
a link to the rules and found in view-scorce

line 273
flag{90bc54705794a62015369fd8e86e557b}

  # Rules

We don't want to have to enforce restrictions on you, but there are a few things we would like to politely ask you not to do:

1.  Please do not attack the competition infrastructure or other players. The challenges are your targets. That's it.
2.  You do not need to use automated scanners like `sqlmap`, DirBuster, `nmap`, Metasploit, `nikto` or others. Please do not use them against the challenges.
3.  Please do not brute-force flags.
4.  Please do not share flags with other players, or explicitly and deliberately cheat.
5.  **Please do not blatantly ask for hints.** The proper to way to ask for help is to explain what you have tried and showcase_(in a direct message)_ what errors or output you may have.

##### Flag Format

Flags for this competition will follow the format: **`flag\{[0-9a-f]{32}\}`**. That means a `flag{}` wrapper with what looks like an MD5 hash inside the curly braces. If you look closely, you can even find a flag on this page!

##### Support

For admin support in the case of any technical issues, please join the `NahamSec` Discord server: [https://discord.gg/nahamsec-598608711186907146](https://discord.gg/nahamsec-598608711186907146).

You should find a `#ctf-general` channel in the **NahamCon 2023** category and direct your questions there. When your question requires discussing a specific challenge, please direct message one of the challenge authors as noted in the challenge description.

### ![](https://johnhammond.org/static/misc/nahamcon2023_sponsors.png)
Ctrl + U 
<!DOCTYPE html>
<html lang="en">
...	<title>NahamCon CTF 2023</title>
...
<!-- Thank you for reading the rules! Your flag is: -->
<!--   flag{90bc54705794a62015369fd8e86e557b}       -->
...
</html>

## Fast Hands 
Author: @JohnHammond#6971  
  You can capture the flag, but you gotta be fast!  
  **Press the `Start` button on the top-right to begin this challenge.**
**Connect with:**  
-   [http://challenge.nahamcon.com:31565](http://challenge.nahamcon.com:31565)
**Please allow up to 30 seconds for the challenge to become available.**
 ### Description 
 This challenge had a button that when you click it it would open a new tab and then immediately disrepair, I tried to use inspect in the web browser but found it was much easier to use burp to find that this was in the body of the web page 
 
in the body of the page found 
`</head>

<body>

<!-- Page content-->

<div class="container p-5">

<div class="text-center mt-5 p-5">

<button type="button" onclick="ctf()" class="btn btn-primary"><h1>Capture The Flag</button>

</div>

</div>

<!-- Bootstrap core JS-->

<script src="[https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js](https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js)"></script>

</body>

<script type="text/javascript">

function ctf() {

window.open("./capture_the_flag.html", 'Capture The Flag', 'width=400,height=100%,menu=no,toolbar=no,location=no,scrollbars=yes');

}

</script>

</html>
`
then used that in burp to get the page to load

` <span style="display:none">
                  flag{80176cdf1547a9be54862df3568966b8}
                </span></button>
            </div>


## Online Chatroom 
Author: @JohnHammond#6971  
  
We are on the web and we are here to chat!  
  
**Press the `Start` button on the top-right to begin this challenge.**

**Please allow up to 30 seconds for the challenge to become available.**

**Attachments:**

from attachmen
`package main

import (
	"flag"
	"io/ioutil"
	"log"
	"net/http"
	"strconv"
	"strings"
	"time"

	"github.com/gorilla/websocket"
	"github.com/rs/cors"
)

var addr = flag.String("addr", "0.0.0.0:8080", "http service address")

var upgrader = websocket.Upgrader{
	CheckOrigin: func(r *http.Request) bool { return true },
}

var chatHistory []string

var onlineUsers = []string{
	"User0",
	"User1",
	"User2",
	"User3",
	"User4",
}

func echo(w http.ResponseWriter, r *http.Request) {
	c, err := upgrader.Upgrade(w, r, nil)
	if err != nil {
		log.Print("upgrade:", err)
		return
	}
	defer c.Close()


	for {
		_, message, err := c.ReadMessage()
		if err != nil {
			log.Println("read:", err)
			break
		}
		log.Printf("recv: %s", message)

		if strings.HasPrefix(string(message), "!") {
			switch {
			case strings.HasPrefix(string(message), "!history"):
				indexStr := strings.TrimPrefix(string(message), "!history ")
				index, err := strconv.Atoi(indexStr)
				if err != nil || index < 1 || index > len(chatHistory) {
					c.WriteMessage(websocket.TextMessage, []byte("Error: Please request a valid history index (1 to "+strconv.Itoa(len(chatHistory)-1)+")"))
					continue
				}
				err = c.WriteMessage(websocket.TextMessage, []byte(chatHistory[len(chatHistory)-index]))
				if err != nil {
					log.Println("write:", err)
					break
				}
			case strings.HasPrefix(string(message), "!date"):
				response := "<pre>Server date is: " + time.Now().Format(time.RFC3339) + "</pre>"
				err = c.WriteMessage(websocket.TextMessage, []byte(response))
				if err != nil {
					log.Println("write:", err)
					break
				}
			case strings.HasPrefix(string(message), "!users"):
				response := "<pre>Online users: " + strings.Join(onlineUsers, ", ") + "</pre>"
				err = c.WriteMessage(websocket.TextMessage, []byte(response))
				if err != nil {
					log.Println("write:", err)
					break
				}
			case strings.HasPrefix(string(message), "!help"):
				response := "<pre>Commands:\n" +
					"!write [message]: send a message\n" +
					"!date: get the server date\n" +
					"!users: get the online users\n" +
					"!help: list available commands</pre>"
				err = c.WriteMessage(websocket.TextMessage, []byte(response))
				if err != nil {
					log.Println("write:", err)
					break
				}
			case strings.HasPrefix(string(message), "!write"):
				message = []byte(strings.TrimPrefix(string(message), "!write "))
				chatHistory = append(chatHistory, "User0: " + string(message))
				err = c.WriteMessage(websocket.TextMessage, []byte("User0: "+string(message)))
				if err != nil {
					log.Println("write:", err)
					break
				}
			default:
				response := "Invalid command. Type !help for a list of commands."
				err = c.WriteMessage(websocket.TextMessage, []byte(response))
				if err != nil {
					log.Println("write:", err)
					break
				}
			}
		} else {
			response := "Invalid command. Type !help for a list of commands."
			err = c.WriteMessage(websocket.TextMessage, []byte(response))
			if err != nil {
				log.Println("write:", err)
				break
			}
		}
	}
}

func allHistory(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte(strconv.Itoa(len(chatHistory)-1)))
}

func main() {
	flag.Parse()
	log.SetFlags(0)

	flagData, err := ioutil.ReadFile("flag.txt")
	if err != nil {
		log.Fatal("Failed to read flag file:", err)
	}
	flagStr := string(flagData)
	chatHistory = append(chatHistory, "User5: Aha! You're right, I was here before all of you! Here's your flag for finding me: " + flagStr)

	extraChatMessages := []string{
		"User4: This chat is awesome!",
		"User1: I agree, it's really cool.",
		"User2: I'm enjoying it too!",
		"User3: Me too! Great conversations happening here.",
		"User1: Wait, has someone been here before us?",
		"User2: Oh hey User0, was it you? You can use !help as a command to learn more :)",
	}
	chatHistory = append(chatHistory, extraChatMessages...)

	mux := http.NewServeMux()

	mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		http.ServeFile(w, r, "index.html")
	})

	mux.HandleFunc("/chatroom.js", func(w http.ResponseWriter, r *http.Request) {
		http.ServeFile(w, r, "chatroom.js")
	})

	mux.HandleFunc("/echo", echo)
	mux.HandleFunc("/allHistory", allHistory)

	handler := cors.Default().Handler(mux)
	log.Fatal(http.ListenAndServe(*addr, handler))
}`

In the code i found 
`func main() {
	flag.Parse()
	log.SetFlags(0)

	flagData, err := ioutil.ReadFile("flag.txt")
	if err != nil {
		log.Fatal("Failed to read flag file:", err)
	}
	flagStr := string(flagData)
	chatHistory = append(chatHistory, "User5: Aha! You're right, I was here before all of you! Here's your flag for finding me: " + flagStr)`
so i tryed 

!history 
Error: Please request a valid history index (1 to 11)

!history 12 
User5: Aha! You're right, I was here before all of you! Here's your flag for finding me: flag{c398112ed498fa2cacc41433a3e3190b}


## Glasses
Author: @JohnHammond#6971  
  
Everything is blurry, I think I need glasses!  
  
**Press the `Start` button on the top-right to begin this challenge.**

**Connect with:**  

-   [http://challenge.nahamcon.com:31468](http://challenge.nahamcon.com:31468)

**Please allow up to 30 seconds for the challenge to become available.**

This took me a bit of time to find in the html 

flag½₧8084e4530cf649814456f2a291eb81e9½―

in the code the space between the paragraphs 

flag{8084e4530cf649814456f2a291eb81e9}

## Technical Support
Author: @JohnHammond#6971  
  
Want to join the party of GIFs, memes and emoji spam? Or just want to ask a question for technical support regarding any challenges in the CTF?  
  
**This CTF uses support tickets to help handle requests. If you need assistance, please create a ticket with the `ctf-open-ticket` channel. Do not bother DM-ing organizers or facilitators, they will just tell you to open a ticket. You might find a flag in the ticket channel, though!  
  
**Connect here:**  
[Join the Discord!](https://discord.gg/nahamsec-598608711186907146)**

on the support page 
Use this channel to open a support ticket during the NahamCon 2023 CTF! flag{a98373a74abb8c5ebb8f5192e034a91c}

## Geosint 1
Head over to [https://osint.golf](https://osint.golf) and select **`chall1` under the NahamCon2023** category. Make sure to read the directions if you are unfamiliar with Geosint/Geoguessr!  
  
**Connect here: [https://osint.golf](https://osint.golf)**
### chall1

yes, flag{3e3d01a002db29fec2a5e10ec758b852}

from Google maps 
16.320547, 102.825139
Tha Phra, Mueang Khon Kaen District, Khon Kaen 40260, Thailand

## Ninety One - not solved
Author: @JohnHammond#6971  
  
I found some more random gibberish text. Why are there so many of these?! (Don't answer that, I know why...)  
  
`@iH<,{|jbRH?L^VjGJH<vn3p7I,x~@1jyt>x?,!YAJr*08P`

## Thank you to all who put this event on and I look foreword to doing better next year
