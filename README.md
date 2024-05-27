# The Google for Logs [ Splunk ]
![image](https://miro.medium.com/v2/resize:fit:720/format:webp/0*bffuqd2qFy3-h377.png)

At this point you can download Splunk from the following link:
[link](http://www.splunk.com/en_us/download.html)

Access Splunk web interface at port 8000 in your local machine.
![image](https://miro.medium.com/v2/resize:fit:2000/format:webp/1*iQMqKl5KEzhyyXCbPemtbg.png)

Splunk platform works with any data. In particular, it works with all IT streaming and historical data. The source of the data can be event logs, web logs, live application logs, network feeds, system metrics, change monitoring, message queues, archive files, and so on. All of them can be stored by upload via panel:

![image](https://miro.medium.com/v2/resize:fit:484/format:webp/1*CD5l-JO2KwQBvkLlzZvj1g.png)

When you start search by clicking on the green button, Splunk takes you to the page where the magic really begins! \o/

![image](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*36HRWrQSq7nRZEoi-1kP-A.png)

Here you can customize the logs information whatever you want!

- Choose the info that matters.
- Select the period of time that the logs have occurred.
- Change the log type.
And lot of other stuffs.

![image](https://miro.medium.com/v2/resize:fit:720/format:webp/1*TgUgepomEUqnOfwObwQmMQ.png)

So let’s start Googling!

The Search bar will be your basis when you become familiar with the syntax language of Splunk. The search assistant could be a great friend to be within the right terms.

![image](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*1rSFYuUoCrtTPOPOuc149A.png)

You start with the name of the project or something related to a project that you want to search and apply the syntax. For example, to find some error, fail and severe log you can type:

```bash
“you project name” (without quotes) (error OR fail* OR severe)
```
The asterisk (*) character is used to match various terms that begins with the previous word, in this case “fail”. The “OR” is like on any syntax that understands by itself as a simple “or” preposition.

The Events tab displays the Timeline of events, the Fields sidebar, and the Events viewer.

At this one you can select any kind of info you want to be showed by a log.

![image](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*VwTZtLP5jy5382EZhPMLpA.png)

You can also use Splunk with Docker integration. As the name by itself set, here you’ll use an image provided by outcouldman to add Splunk as a Docker container:

````bash
docker pull outcoldman/splunk:latest
````
or add it manually:
````bash
git clone https://github.com/outcoldman/docker-splunk.git
cd docker-splunk/splunk
docker build --tag="$USER/splunk"
````

You can also start the container manually:

````bash
docker run --name vsplunk -v /opt/splunk/etc -v /opt/splunk/var busybox
docker run --hostname splunk --name splunk --volumes-from=vsplunk -p 8000:8000 -d --env SPLUNK_START_ARGS="--accept-license" outcoldman/splunk:latest
````
You need to run Busybox container for Splunk security container, if it fails, restarts or stops.

Or if you use Docker Compose:

````bash
vsplunk:
  image: busybox
  volumes:
    - /opt/splunk/etc
    - /opt/splunk/var
splunk:
  image: outcoldman/splunk:latest
  hostname: splunk
  volumes_from:
    - vsplunk
  ports:
    - 8000:8000
````
It’s interesting to know about the Splunk ports and your respective functions:

- 8000/tcp — Splunk Web interface (Splunk Enterprise and Splunk Ligh
- 8089/tcp — Splunk Services (All Splunk products)
- 8191/tcp — Application KV Store (Splunk Enterprise)
- 9997/tcp — Splunk Indexing Port (not used by default) (Splunk Enterprise)
- 1514 — Network Input (not used by default) (All Splunk products)
- 8088 — HTTP Event Collector

In order to let Splunk reads the docker containers we need to add a Token to HTTP Event Collector via web interface:

> - Go to: **Settings > Data Inputs.**

![image](https://miro.medium.com/v2/resize:fit:640/format:webp/1*nIYyJNEY8K66NrDATsNCEQ.png)

````bash
After that click on HTTP Event Collector.

At the top go to Global Settings and select Enabled to All Tokens field. Then click on Save.

Click on New Token, give it a name and follow the steps.
````

Here we are… Ready to receive logs from docker through this token.

Now we are ready to test the Splunk logging driver. You can configure the logging driver for the whole Docker daemon or per container. For this example, I am going to use the outcoldman/splunk container and configure it for the container:

````bash
docker run --publish 80:80\
 --log-driver=splunk\
 --log-opt splunk-token=9B0A57407-BF30–4183-BC9C-0EBC73A6E100\
 --log-opt splunk-url=https://localhost:8000\
 --log-opt splunk-insecureskipverify=true\
 outcoldman/splunk:latest
````
So let’s send some request to the url to generate some logs output:

````bash
curl localhost:80
curl localhost:80?testing-my-splunk-logs
````

> - Back to your Search and Reporting Splunk interface and search for the Docker logs by the name you set to your Token:
````bash
source = "your-token-name" (with quotes)
````
![image](https://miro.medium.com/v2/resize:fit:1100/format:webp/1*5xl_mtIfWs1wReiVx3_ARA.png)
