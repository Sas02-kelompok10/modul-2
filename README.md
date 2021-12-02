
#  modul 2  
## Rubah LXC landing dengan ubuntu focal (destroy n create, same ip, same name)

- Pertama, kita hapus terlebih dahulu untuk container ubuntu_php7.4 dan ubuntu_landing dan backupannya. karena kita tidak bisa membuat container baru dengan nama yang sama.
    - `Sudo lxc-destroy ubuntu_php7.4`
    - `Sudo lxc-destroy ubuntu_landing`
    - ![Capture2](https://user-images.githubusercontent.com/93419670/144343920-f17c7584-8a5c-4359-bd3f-3177b2577908.PNG)
    - ![Capture10](https://user-images.githubusercontent.com/93419670/144346440-14cb7544-c868-45b4-bf2c-8c193d04a556.PNG)


- setelah itu, buat baru container lxc ubuntu landing dan php7.4 versi focal 
    - `sudo lxc-create -n ubuntu_landing -t download -- --dist ubuntu --release focal --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org`
    - ![Capture9](https://user-images.githubusercontent.com/93419670/144345950-f1d88949-1377-4e72-bfa0-a969dd574482.PNG)
    - ![Capture10](https://user-images.githubusercontent.com/93419670/144345968-d94536ea-d570-4943-8fbf-263fb5fd89da.PNG)
    
    - `sudo lxc-create -n ubuntu_php7.4 -t download -- --dist ubuntu --release focal --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org`
    - ![Capture4](https://user-images.githubusercontent.com/93419670/144346736-e2e35151-9453-4666-ae54-f82f00b5362f.PNG)


- Seperti biasanya, kita masuk kedalam container. lalu lakukan update komponen lxc ke versi terbaru serta install perintah nano.
    - `apt install nano net-tools curl`
    - ![Capture13](https://user-images.githubusercontent.com/93419670/144345395-570c95a0-7db3-4bb8-9d3b-6159c12dc9c3.PNG)

- Untuk mempermudah konfigurasi ansible kita perlu lakukan konfigurasi Ip static, ini akan menjadikan ip tidak akan berubah”.
    - `sudo nano /etc/netplan/10-lxc.yaml`
    - ![Capture5](https://user-images.githubusercontent.com/93419670/144344290-2e25e576-aa9d-4766-8ee3-abf4b31940ef.PNG)
    - ![Capture11](https://user-images.githubusercontent.com/93419670/144346974-4213c1b0-01b0-446d-9a8e-ada5f9fbfb93.PNG)

- selanjutnya, untuk mempermudah mengontrol container kita perlu melakukan instlasi open ssh server. open ssh ini berfungsi sebagai media transfer data dengan aman yang bisa digunakan secara remote atau dari jarak jauh. 
    - `apt install openssh-server`
    - ![Capture7](https://user-images.githubusercontent.com/93419670/144344394-ebe268ac-2eea-4ffa-8b76-de57211d4d6f.PNG)

 ## Konfigurasi ssh
 - Konfigurasi password container lxc, Membuat Password Baru
    - `password`
    - ![Capture14](https://user-images.githubusercontent.com/93419670/144344621-be02f503-d47b-4a99-ac17-6e07b640b115.PNG)
    - `ssh root@10.0.3.101`
    - ![Capture15](https://user-images.githubusercontent.com/93419670/144347379-b34273b8-f5a5-4f9d-aecf-87417a21a419.PNG)
    - `ssh root@10.0.3.103`
    - ![Capture16](https://user-images.githubusercontent.com/93419670/144347425-39d64374-9d60-4a0e-b07f-bcc29430c2fc.PNG)

 
 - install laravel
    - ![Capture17](https://user-images.githubusercontent.com/93419670/144345086-b6ad0d1d-4069-4493-b198-13348afcdf39.PNG)
 - making directory
    - `mkdir -p roles/php/tasks`
    - `mkdir -p roles/php/handlers`
    - ![Capture19](https://user-images.githubusercontent.com/93419670/144348169-15d44bdb-17b9-4b89-9749-6cdbe540ef4c.PNG)
    - 
    - cd roles/php/tasks; nano main.yml
    - ![Capture21](https://user-images.githubusercontent.com/93419670/144348289-0a0a63e2-a60d-409a-a1fd-76e8149c40ed.PNG)
    - 
    - cd roles/php/handlers; nano main.yml
    - ![Capture22](https://user-images.githubusercontent.com/93419670/144348566-99e26a9c-4c27-4726-b683-a84ac4465fa3.PNG)

 - Isi roles/lv/tasks
    - ~/ansible/modul2-ansible$ cd roles/lv/tasks/
    - ~/ansible/modul2-ansible/roles/lv/tasks$ nano main.yml
    - ![image](https://user-images.githubusercontent.com/93419670/144348883-4c386e86-379d-4ece-a40b-b0fdcd074b72.png)



   
