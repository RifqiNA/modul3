# **Module 3 Practicum Report**

## Group 12 [IT02-01]

**Rifqi Naufal A 1202190012 || Andi Tadang P 1202190046**

<hr> 

### **Study Case**

Create SubDomain dev.vm.local with rules :

* Ansible
* Same lxc like vm.local
* The folder must be in var/www/html/dev{name_app}



### **Problem Solving**

* The first step is to go to the directory using ansible

  ```
  cd ansible/modul2/ansible/laravel
  nano sublaravel.yml
  ```
  ![1](https://user-images.githubusercontent.com/93064971/146403785-22b638b8-6548-4a8b-ab52-824caf6bca58.png)
  
  ```
  ---
  - hosts: all
    become : yes
    tasks:
      - name: install bind9 dan dnsutils
        apt:
         pkg:
           - bind9
           - dnsutils
  ```
  
* The next step is install packages with ansible

  ![2](https://user-images.githubusercontent.com/93064971/146403985-0883d077-e19b-4be1-97fa-0b423e02cd6b.png)
  
* Create e configsub.yml file

  ![3](https://user-images.githubusercontent.com/93064971/146404153-b431d58f-9ac1-4863-872f-bbf53880448f.png)

  ```
  ---
  - hosts: all
    become : yes
    tasks:
     - name: membuat direktori
       file:
        path: /var/www/html/dev/landing
        state: directory
     - name: copy file vm.local
       copy:
        src: /etc/bind/vm/vm.local
        dest: /var/www/html/dev/landing
     - name: mengganti konfigurasi
       replace:
        path: /var/www/html/dev/landing/vm.local
        regexp: 'www'
        replace: 'dev'
     - name: copy file named.conf.local
       copy:
        src: /etc/bind/named.conf.local
        dest: /etc/bind/named.conf.local
     - name: mengganti konfigurasi conf local
       replace:
        path: /etc/bind/named.conf.local
        regexp: '/etc/bind/vm/vm.local'
        replace: '/var/www/html/dev/landing/vm.local'
     - name: mengganti konfigurasi conf local part2
       replace:
        path: /etc/bind/named.conf.local
        regexp: '/etc/bind/vm/1.168.192.in-addr.arpa'
        replace: '/var/www/html/dev/landing/1.168.192.in-addr.arpa'
     - name: copy file 1.168.192.in-addr.arpa
       copy:
        src: /etc/bind/vm/1.168.192.in-addr.arpa
        dest: /var/www/html/dev/landing
     - name: copy file resolv.conf
       copy:
        src: /etc/resolv.conf
        dest: /etc/resolv.conf
     - name: copy file named.conf.options
       copy:
        src: /etc/bind/named.conf.options
        dest: /etc/bind/named.conf.options
     - name: restart nginx
       service:
        name: nginx
        state: restarted
     - name: restart bind9
       action: service name=bind9 state=restarted
    ```
    
  * Do the installation using the script below

    ![4](https://user-images.githubusercontent.com/93064971/146404332-fd234eca-e380-4af3-86ba-d625cad8ec23.png)

  * Add subdomain to /etc/hosts

    ![5](https://user-images.githubusercontent.com/93064971/146404600-a81e305e-a101-4fa8-9851-2771df92a95f.png)

  * Open vm.local file

    ```
    ssh root@lxc_landing.dev
    nano /var/www/html/dev/landing/vm.local
    ```

  * Add line www. After that exit lxc

    ![6](https://user-images.githubusercontent.com/93064971/146404752-a93dff66-a1dc-4e45-90f8-316fa10c7207.png)

  * Open and edit vm.local in directory /etc/nginx/sites-enabled/

    ```
    cd /etc/nginx/sites-enabled/
    nano vm.local
    ```
    
    ![7](https://user-images.githubusercontent.com/93064971/146405319-84095db5-ae45-41bb-9ec3-999d03d41858.png)

  * Open and edit vm.local in directory /etc/bind/vm/

    ![8](https://user-images.githubusercontent.com/93064971/146405464-7f4f08ff-239e-4d99-8c41-9594a552d351.png)

  * Restart all packages

    ![9](https://user-images.githubusercontent.com/93064971/146405546-97f3061d-338e-4646-988e-76cc8b22d00c.png)

  * Open Wifi Settings (in the case we use linux os), add dns server and uncheck automatic at menu Ipv4

    ![24](https://user-images.githubusercontent.com/93064971/146405658-17544475-fa5f-419b-addc-989def43a6c9.png)

  * At menu IPv6, check disabled (we must turn off the IPv6), and then click apply
  
  * Just reconnecting the wifi, and check dev.vm.local on the browser :D

    ![image](https://user-images.githubusercontent.com/93064971/146416726-0d837a92-07ac-44bb-9232-5e23151d0796.png)
    
