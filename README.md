
#  modul 2  
## Rubah LXC landing dengan ubuntu focal (destroy n create, same ip, same name)
![image](https://user-images.githubusercontent.com/93419670/144250777-ed6299c5-aad1-4674-b717-b298ca073f3d.png)

   - Pertama, kita hapus terlebih dahulu untuk container ubuntu_php7.4 dan backupannya. karena kita tidak bisa membuat container baru dengan nama yang sama.
   - Sudo lxc-destroy ubuntu_php7.4
   - ![image](https://user-images.githubusercontent.com/93419670/144250858-e1aab45f-2edb-488d-b7f0-10b169b02742.png)



   - nah setelah itu, buat baru container lxc ubuntu versi focal 
   - `sudo lxc-create -n ubuntu_php7.4 -t download -- --dist ubuntu --release focal --arch amd64 --force-cache --no-validate --server images.linuxcontainers.org`
   - ![image](https://user-images.githubusercontent.com/93419670/144251009-dd6e9744-b7e1-42f9-9dbe-1636cd1c8f09.png)
   - ![image](https://user-images.githubusercontent.com/93419670/144251071-a49dd90e-54f2-4dfc-85d1-f0159db640d8.png)
   - ![image](https://user-images.githubusercontent.com/93419670/144251125-7994155a-58c3-4a3c-bcae-d80c225151ca.png)

   - Seperti biasanya, kita masuk kedalam container. lalu lakukan update komponen lxc ke versi terbaru serta install perintah nano.



   - Untuk mempermudah konfigurasi ansible kita perlu lakukan konfigurasi Ip static, ini akan menjadikan ip tidak akan berubah”.

   - `sudo nano /etc/netplan/10-lxc.yaml`
   - ![image](https://user-images.githubusercontent.com/93419670/144251239-f0e7a504-3a29-4622-a37c-943058a3749f.png)
   - ![image](https://user-images.githubusercontent.com/93419670/144251310-d01a7363-70a8-44a2-a9dd-5252ce2cf179.png)


- selanjutnya, untuk mempermudah mengontrol container kita perlu melakukan instlasi open ssh server. open ssh ini berfungsi sebagai media transfer data dengan aman yang bisa digunakan secara remote atau dari jarak jauh. 

    - `apt install openssh-server`
    - ![image](https://user-images.githubusercontent.com/93419670/144251459-b1e99c6c-2b3e-42b1-8d0a-3d6ba2bdf8f9.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144251526-bc561075-5bb9-4d12-8a48-c7499fcfc9e9.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144251573-6ca4cb43-5d32-4b97-b6c8-44de76107f35.png)


## Konfigurasi ssh
- ![image](https://user-images.githubusercontent.com/93419670/144251660-875936e5-26b6-446d-ad40-5f7db3abd3e1.png)
- ![image](https://user-images.githubusercontent.com/93419670/144251711-23a9cf22-8cd0-4cf9-af9a-8e676c5db0c5.png)





    - Konfigruasi password container lxc, Membuat Password Baru
    - `password`
    - ![image](https://user-images.githubusercontent.com/93419670/144251810-2ec2722e-7a93-4b88-9f94-8b46b615ed5b.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144251853-e8deec44-dfc5-48d4-9718-fba3ada676a0.png)
    
    - Pada Host, tambahkan ip serta domain yang kita digunakan pada lxc container
    - ![image](https://user-images.githubusercontent.com/93419670/144251954-3d439e88-de40-4d40-a915-65f1d360a97a.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144252012-175629f9-99bb-45d8-9de1-4d62c90fa8bf.png)

    ### Membuat File instalasi wordpress, file ini akan menjalankan proses instalasi pada ansible


    - Pada roles wordpress kita perlu membuat folder baru (tasks, tempalate,hendles). Folder handlers berisi perintah perintah untuk menjalankan wordpress seperti restart, sedangkan folder tasks berisi script instalasi wordpress dan folder templates berisi template konfigurasi untuk wordpress.
    - ![image](https://user-images.githubusercontent.com/93419670/144252168-4c049cec-3bee-4ea5-8c0d-f541e88aeb7c.png)
    ### tasks
    - ![image](https://user-images.githubusercontent.com/93419670/144258903-abf8f540-66a1-404a-a02c-1bb03bc7ecb4.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144258944-1c9c2365-3f4f-4528-9ef8-cb71c65d992a.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144259007-bac628f4-3685-4b9f-9f00-2d363a5a6da4.png)

    ### handlers
    - ![image](https://user-images.githubusercontent.com/93419670/144259062-6fac3ea1-68f3-4e75-8e7f-6317f7f00a86.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144259189-9aca17ec-ebe6-49f8-94de-9934303cacb3.png)
    ### templates wp.conf
    - ![image](https://user-images.githubusercontent.com/93419670/144258674-9de6c496-cb0e-4b8a-8fb0-96285878acac.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144258750-c1c02748-977c-415b-bfdb-4fd24d07c071.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144258801-d489e87b-e312-400b-8a31-cff4e19ff778.png)

    ### templates wordpress.conf
    - ![image](https://user-images.githubusercontent.com/93419670/144258487-f4d90e48-3b13-4a3a-9388-69dd49a264e4.png)

    ### Main In the middle
    - ![image](https://user-images.githubusercontent.com/93419670/144258352-f69b2f1a-d51a-4752-8ede-d562904fe3dd.png)
    ### Memperbaiki Main In The middle
    - ![image](https://user-images.githubusercontent.com/93419670/144258192-cd048e07-24b8-419e-bdba-9c6a4a11a8a0.png)
    - Lalu kita perlu menghapus konfigurasi ssh baris 3 dan 8, untuk memudahkan konfigruasi ansible container
    - ![image](https://user-images.githubusercontent.com/93419670/144257871-7dfd37a1-4258-494d-b573-3b663c9df14a.png)
    ### setelah dihapus
    - ![image](https://user-images.githubusercontent.com/93419670/144257952-700323b5-8533-4209-a4f5-609309d09fe8.png)
    - Setelah di hapus, jalankan ulang root@lxc_php7.dev
    ### Menjalankan Instalasi ansible wordpress
    - ![image](https://user-images.githubusercontent.com/93419670/144257583-806dea28-400c-4946-8cde-34f3f966b2f1.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144257737-a00699a8-68ec-4fa7-b91e-d272ee377c65.png)
    ### Halaman Utama Wordpress
    - ![image](https://user-images.githubusercontent.com/93419670/144256856-19838e11-1c42-4dc0-b86d-69104ce79de4.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144256940-ab4555e6-51a3-472e-a4ae-0abb9c2b1f82.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144257070-a14ad758-3fa8-442c-8fdc-b456fea704be.png)
    
    ### Dashboard
    - ![image](https://user-images.githubusercontent.com/93419670/144256683-de4df336-4fa1-4b8a-becc-599e1cce1a63.png)
    - database yang digunakan wordpress
    - ![image](https://user-images.githubusercontent.com/93419670/144256495-d7bd7a48-751f-4744-857f-5bbace8df19e.png)
    - ![image](https://user-images.githubusercontent.com/93419670/144256411-0a2f4530-2ae5-4308-8e0f-f85d1e89282d.png)
