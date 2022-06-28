## training plan 

<img src="cna.png">

### Serverless

<img src="lambda.png">

### FAAS 

<img src="faas.png">

### lambda function view 

<img src="view.png">

### compsoe file 

```
version: "3.8" # it is automatically going to create a docker network bridge 
services: # your application stack 
  ashufrontend:
   image: wordpress # image from docker hub already have a webapp 
   container_name: ashuc1 
   restart: always 
   ports:
     - 1234:80 # expose application to external world 
   environment:
     WORDPRESS_DB_HOST: ashubackdb 
     WORDPRESS_DB_USER: ashuuser
     WORDPRESS_DB_PASSWORD: "Cisco123456#"
     WORDPRESS_DB_NAME: ashudb
   depends_on:  # it will wait for db container to be in ready state 
     - ashubackdb
  ashubackdb: 
   image: mysql:5.7
   container_name: ashucdb1 
   restart: always 
   environment:
    MYSQL_DATABASE: ashudb  
    MYSQL_USER: ashuuser
    MYSQL_PASSWORD: "Cisco123456#"
    MYSQL_RANDOM_ROOT_PASSWORD: "1"
   volumes: # mount volume 
    - ashudbvol:/var/lib/mysql/  
#networks
volumes: # creating docker volume 
 ashudbvol: # name of volume
```

### services check 

```
docker-compose  up -d 
[+] Running 2/2
 ⠿ ashubackdb Pulled                                                                                                       2.6s
 ⠿ ashufrontend Pulled                                                                                                     2.7s
[+] Running 4/4
 ⠿ Network ashuapp_default     Created                                                                                     0.2s
 ⠿ Volume "ashuapp_ashudbvol"  Created                                                                                     0.0s
 ⠿ Container ashucdb1          Started                                                                                     3.9s
 ⠿ Container ashuc1            Started                                                                                     3.0s
[root@ip-172-31-0-119 ashuapp]# docker  network ls
NETWORK ID     NAME                  DRIVER    SCOPE
d098f5e9c8b7   ashuapp_default       bridge    local
ce7cf889a5a4   bridge                bridge    local
791f31ad26d1   host                  host      local
f899626f209b   none                  null      local
53e0f9ec1127   ravi_default          bridge    local
0f17d8d8d21a   saidul_default        bridge    local
717880c19ac6   saththirapp_default   bridge    local
[root@ip-172-31-0-119 ashuapp]# docker-compose  ps
NAME                COMMAND                  SERVICE             STATUS              PORTS
ashuc1              "docker-entrypoint.s…"   ashufrontend        running             0.0.0.0:1234->80/tcp, :::1234->80/tcp
ashucdb1            "docker-entrypoint.s…"   ashubackdb          running             3306/tcp, 33060/tcp
[root@ip-172-31-0-119 ashuapp]# 


```

### sample check 

```

[root@ip-172-31-0-119 ashuapp]# docker exec -it  ashucdb1  bash 
root@0ba2c311c80d:/# cd /var/lib/mysql
root@0ba2c311c80d:/var/lib/mysql# ls
ashudb	  ca-key.pem  client-cert.pem  ib_buffer_pool  ib_logfile1  ibtmp1  performance_schema	public_key.pem	 server-key.pem
auto.cnf  ca.pem      client-key.pem   ib_logfile0     ibdata1	    mysql   private_key.pem	server-cert.pem  sys
root@0ba2c311c80d:/var/lib/mysql# ls  ashudb 
db.opt		    wp_links.frm     wp_postmeta.ibd		wp_term_taxonomy.frm  wp_terms.ibd
wp_commentmeta.frm  wp_links.ibd     wp_posts.frm		wp_term_taxonomy.ibd  wp_usermeta.frm
wp_commentmeta.ibd  wp_options.frm   wp_posts.ibd		wp_termmeta.frm       wp_usermeta.ibd
wp_comments.frm     wp_options.ibd   wp_term_relationships.frm	wp_termmeta.ibd       wp_users.frm
wp_comments.ibd     wp_postmeta.frm  wp_term_relationships.ibd	wp_terms.frm	      wp_users.ibd
root@0ba2c311c80d:/var/lib/mysql# exit
exit

```

### problem with containers -- 

<img src="prob_container.png">

### CNA -- new approach 

<img src="cna.png">

### k8s architecture 

<img src="k8sarch.png">

### Swarm and k8s 

<img src="k8s1.png">

### OCI and RUNC 

<img src="oci.png">

### OCI and CRI 

<img src="cir.png">




