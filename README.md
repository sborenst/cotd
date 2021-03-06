
COTD: Cat of the Day

php web application using JQuery Mobile that publishes a list of ordered items. 
Each item has an image and trivia associated with it.
Cats are used as a default sample list, but lists of abritary items and length can be used.

# Website

A sample running application is hosted on OpenShift Online V2.

    http://www.cotd.net.au

# Configuration

Lists can be customed by editing the contents in the data subdirectory.
Edit selector.php to point to subdirectory name of the list of interest.
Edit the rank.php file under the subdirectory of the selected list.
Add images as required in the images subdirectory with names matching list items.

# Logging

Whenever the user rates an item, an entry is written to the php log.
These entries can be filtered and then used to test hypotheses regarding user engagement.
An example entry is as follows:

    [Sun Sep 25 09:14:40.037909 2016] [:error] [pid 15] [client 172.17.0.1:46572] <COTD> { "user" : "e299ra835usa88pp19sr25ipg6", items" : [ {"adelaide" : "1"}, {"canberra" : "3"}, ] , "client_ip" : "172.17.0.1",  "sydney_time" : "2016:09:25 19:14:40",  } </COTD>, referer: http://localhost:8080/item.php?nextpage=canberra

# AB Deployment Example

To experiment with an A/B deployment scenario, follow the instructions in the blog entry at: https://blog.emergile.com/2016/10/07/ab-deployments-made-easy-with-openshift/  

# Running using Docker Toolbox

    $ docker pull spicozzi/cotd
    $ docker run -d -i -p 8080:80 spicozzi/cotd
    Browser http://localhost:8080

# Running on Openshift3

    oc new-project cotd --display-name="City of the day" --description='City of the day'
    oc new-app openshift/php:5.6~https://github.com/<repo>/cotd.git
    oc expose svc cotd

# Developing on the fly in Openshift3

Edit the buildconfig:

    -- change from Git to binary
    source:
      type: Git
      git:
        uri: 'https://github.com/eformat/cotd.git'
      secrets: null

    -- to this
    source:
      type: Binary

    -- then build with
    oc start-build --from-dir=. cotd

You may also wish to enable live reload for php image (don't do this in prod)

    oc set env dc/cotd OPCACHE_REVALIDATE_FREQ=0

# Parse the pods running statistics

For now:

    ./parseCotdLogs.pl $(oc get pods | grep cotd | grep Running | awk '{print $1}')
