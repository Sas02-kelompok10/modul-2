
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

vm.local/

- Langkah pertama yang harus dilakukan yakni menginstall laravel  menggunakan ansible. Setelah menginstall, masuk ke ansible tersebut dan buatlah folder laravel

   ![20](https://user-images.githubusercontent.com/61863147/144441684-ff34a07b-69de-4753-99fc-6ffb6f93b3fe.PNG)

  
- Selanjutnya, buat host untuk lxc yang nanti akan di otomasi

  ![21](https://user-images.githubusercontent.com/61863147/144441808-d6454fc4-e5cd-4e05-8a34-71b11a902a11.PNG)


   

  ```markdown
  [landing]
  ubuntu_landing ansible_host=lxc_landing.dev ansible_ssh_user=root ansible_become_pass=sepasi
  
  ```

  * Buatlah directory serta apapun yang akan digunakan untuk menjalankan folder php dan lakukan instalasi
          	
    ![WhatsApp Image 2021-12-02 at 9 33 51 PM](https://user-images.githubusercontent.com/61863147/144442200-e1da5123-f90b-4f9e-99f6-d87a9c444b49.jpeg)

    

  ```
  ---
  - hosts: all
    become : yes
    tasks:
      - name: install nginx nginx extras
        apt:
         pkg:
           - nginx
           - nginx-extras
         state: latest
      - name: start nginx
        service:
         name: nginx
         state: started
      - name: menginstall tools
        apt:
         pkg:
           - curl
           - software-properties-common
           - unzip
         state: latest
      - name: "Repo PHP 7.4"
        apt_repository:
          repo="ppa:ondrej/php"
      - name: "Updating the repo"
        apt: update_cache=yes
      - name: Installation PHP 7.4
        apt: name=php7.4 state=present
      - name: install php untuk laravel
        apt:
         pkg:
            - php7.4-fpm
            - php7.4-mysql
            - php7.4-mbstring
            - php7.4-xml
            - php7.4-bcmath
            - php7.4-json
            - php7.4-zip
            - php7.4-common
         state: present
  ```

  

  - Jika instalasi telah selesai dilakukan, buat folder installcomposer.yml

    ![WhatsApp Image 2021-12-02 at 9 34 41 PM](https://user-images.githubusercontent.com/61863147/144442376-7d7268cd-0f60-4144-b554-7b5da7b4a3d2.jpeg)

    


  ```
  ---
   -hosts: all
    become : yes
    tasks:
     - name: Download and install Composer
       shell: curl -sS https://getcomposer.org/installer | php
       args:
        chdir: /usr/src/
        creates: /usr/local/bin/composer
        warn: false
     - name: Add Composer to global path
       copy:
        dest: /usr/local/bin/composer
        group: root
        mode: '0755'
        owner: root
        src: /usr/src/composer.phar
        remote_src: yes
     - name: Composer create project
       become_user: root
       composer:
        command: create-project
        arguments: laravel/laravel landing 
        working_dir: /var/www/html
        prefer_dist: yes
       environment:
          COMPOSER_NO_INTERACTION: "1"
     - name: mengkopi file .env.example jadi .env
       copy:
        dest: /var/www/html/landing/.env.example
        src: /var/www/html/landing/.env
        remote_src: yes
     - name: mengganti konfigurasi .env
       lineinfile:
        path: /var/www/html/landing/.env
        regexp: "{{ item.regexp }}"
        line: "{{ item.line }}"
        backrefs: yes
       loop:
        - { regexp: '^(.*)DB_HOST(.*)$', line: 'DB_HOST=10.0.3.200' }
        - { regexp: '^(.*)DB_DATABASE(.*)$', line: 'DB_DATABASE=landing' }
        - { regexp: '^(.*)DB_USERNAME(.*)$', line: 'DB_USERNAME=admin' }
        - { regexp: '^(.*)DB_PASSWORD(.*)$', line: 'DB_PASSWORD= ' }
        - { regexp: '^(.*)APP_URL(.*)$', line: 'APP_URL=http://vm.local' }
        - { regexp: '^(.*)APP_NAME=(.*)$', line: 'APP_NAME=landing' }
     - name: Composer install ke landing
       composer:
         command: install
         working_dir: /var/www/html/landing
       environment:
         COMPOSER_NO_INTERACTION: "1"
     - name: generate php artisan
       args:
        chdir: /var/www/html/landing
       shell: php artisan key:generate
     - name: mengganti permission storage
       file:
        path: /var/www/html/landing/storage
        mode: 0777
        recurse: yes
  
  ```

  

  

  - Lakukan instalasi kembali
  
      ![2021-11-30_6](https://user-images.githubusercontent.com/61863147/144442932-4960a69b-a892-4e5c-b4cd-1e532bfd4165.png)


  - Buatlah file lxc_landing.dev

       ![22](https://user-images.githubusercontent.com/61863147/144443067-3965ad39-c88f-4250-9035-0f9763871e9c.PNG)


    ```
    server {
            listen 80;
    
            root /var/www/html/landing/public;
            index index.php index.html index.htm;
            server_name lxc_landing.dev;
    
            error_log /var/log/nginx/landing_error.log;
            access_log /var/log/nginx/landing_access.log;
    
            client_max_body_size 100M;
            location / {
                    try_files $uri $uri/ /index.php$args;
            }
            location ~\.php$ {
                    include snippets/fastcgi-php.conf;
                    fastcgi_pass unix:run/php/php7.4-fpm.sock;
                    fastcgi_param SCRIPTFILENAME $document_root$fastcgi_script_name;
            }
    }
    ```

    

  - Buatlah file config.yml

   ![23](https://user-images.githubusercontent.com/61863147/144443120-2b2ad19d-4db6-4943-b5f3-62386ce1ad29.PNG)
 

    

    ```
    ---
    - hosts: all
      become : yes
      vars:
        domain: 'lxc_landing.dev'
      tasks:
       - name: stop apache2
         service:
          name: apache2
          state: stopped
          enabled: no
       - name: Write {{ domain }} to /etc/hosts
         lineinfile:
          dest: /etc/hosts
          regexp: '.*{{ domain }}$'
          line: "127.0.0.1 {{ domain }}"
          state: present
       - name: ensure nginx is at the latest version
         apt: name=nginx state=latest
       - name: start nginx
         service:
          name: nginx
          state: started
       - name: copy the nginx config file 
         copy:
          src: ~/ansible/laravel/lxc_landing.dev
          dest: /etc/nginx/sites-available/lxc_landing.dev
       - name: Symlink lxc_landing.dev
         command: ln -sfn /etc/nginx/sites-available/lxc_landing.dev /etc/nginx/sites-enabled/lxc_landing.dev
         args:
          warn: false
       - name: restart nginx
         service:
          name: nginx
          state: restarted
       - name: restart php7
         service:
          name: php7.4-fpm
          state: restarted
       - name: curl web
         command: curl -i http://lxc_landing.dev
         args:
          warn: false
    ```

    

  - Lakukan instalasi

       ![24](https://user-images.githubusercontent.com/61863147/144443261-e6ca8f84-3d87-4ec4-8db7-92eba8f2da3e.PNG)


    

    

  - Cek dengan cara membuka vm.local. Jika sukses, maka tampilannya akan seperti berikut ini :

  ![25](https://user-images.githubusercontent.com/61863147/144443323-1fe9ff51-0a6f-481e-8364-80974108f3dd.PNG)
      
    
    



4. vm.local/blog

   - Seperti pada nomor sebelumnya, langkah pertama dimulai dengan masuk pada folder ansible
  
     ![WhatsApp Image 2021-12-02 at 9 41 20 PM](https://user-images.githubusercontent.com/61863147/144443674-b3136603-7e6c-498d-bdbf-20c4a2c1ec7f.jpeg)

   
     
     
   - Buat host untuk lxc yang nanti akan di otomasi
    
       ![26](https://user-images.githubusercontent.com/61863147/144443579-52b51a5c-e728-4c9f-afa5-92634e001bda.PNG)


   
   ```
   [blog]
   ubuntu_php7.4 ansible_host=lxc_php7.dev ansible_ssh_user=root ansible_become_pass=sepasi
   ```
   
   
   
   - Buat direktori untuk tasks,templates dan handlers di folder wordpress. Lalu, masuk ke dalam folder tasks untuk menginstall paket
   
      ![WhatsApp Image 2021-12-03 at 7 53 17 AM](https://user-images.githubusercontent.com/61863147/144526278-ae1eaa6f-0275-43b8-9239-79250db144fd.jpeg)

   
   ```
   ---
   - hosts: all
     vars:
       domain: 'lxc_php7.dev'
     tasks:
      - name: delete apt chache
        become: yes
        become_user: root
        become_method: su
        command: rm -vf /var/lib/apt/lists/*
   
      - name: install requirement
        become: yes
        become_user: root
        become_method: su
        apt: name={{ item }} state=latest update_cache=true
        with_items:
         - nginx
         - nginx-extras
         - curl
         - wget
         - php7.4
         - php7.4-fpm
         - php7.4-curl
         - php7.4-xml
         - php7.4-gd
         - php7.4-opcache
         - php7.4-mbstring
         - php7.4-zip
         - php7.4-json
         - php7.4-cli
         - php7.4-mysqlnd
         - php7.4-xmlrpc
         - php7.4-curl
   
      - name: wget wordpress
        shell: wget -c http://wordpress.org/latest.tar.gz
   
      - name: tar latest.tar.gz
        shell: tar -xvzf latest.tar.gz
   
      - name: copy folder wordpress
        shell: cp -R wordpress /var/www/html/blog
   
      - name: chmod
        become: yes
        become_user: root
        become_method: su
        command: chmod 775 -R /var/www/html/blog/
   
      - name: copy .wp-config.conf
        copy:
         src=~/ansible/wordpress/wp.conf
         dest=/var/www/html/blog/wp-config.php
   
      - name: copy wordpress.conf
        copy:
         src=~/ansible/wordpress/wordpress.conf
         dest=/etc/nginx/sites-available/{{ domain }}
        vars:
         servername: '{{ domain }}'
   
      - name: Symlink wordpress.conf
        command: ln -sfn /etc/nginx/sites-available/{{ domain }} /etc/nginx/sites-enabled/{{ domain }}
      
      - name: restart nginx
        become: yes
        become_user: root
        become_method: su
        action: service name=nginx state=restarted
   
      - name: Write {{ domain }} to /etc/hosts
        lineinfile:
         dest: /etc/hosts
         regexp: '.*{{ domain }}$'
         line: "127.0.0.1 {{ domain }}"
         state: present
   
      - name: enable module php mbstring
        command: phpenmod mbstring
   
      - name: restart php
        become: yes
        become_user: root
        become_method: su
        action: service name=php7.4-fpm state=restarted
   
      - name: restart nginx
        become: yes
        become_user: root
        become_method: su
        action: service name=nginx state=restarted
   ```
   
   
   
   - Kemudian, masuk ke dalam templates wp.conf yang merupakan tempat configuration pada wordpress
  
       ![28](https://user-images.githubusercontent.com/61863147/144443813-d9b82e9c-98be-42f9-9c4a-d71751024e72.PNG)

  
   
     
   
     ```
     <?php
     /**
      * The base configuration for WordPress
      *
      * The wp-config.php creation script uses this file during the installation.
      * You don't have to use the web site, you can copy this file to "wp-config.php"
      * and fill in the values.
      *
      * This file contains the following configurations:
      *
      * * MySQL settings
      * * Secret keys
      * * Database table prefix
      * * ABSPATH
      *
      * @link https://wordpress.org/support/article/editing-wp-config-php/
      *
      * @package WordPress
      */
     
     define( 'WP_HOME', 'http://vm.local/blog' );
     define( 'WP_SITEURL', 'http://vm.local/blog' );
     
     // ** MySQL settings - You can get this info from your web host ** //
     /** The name of the database for WordPress */
     define( 'DB_NAME', 'blog' );
     
     /** MySQL database username */
     define( 'DB_USER', 'admin' );
     
     /** MySQL database password */
     define( 'DB_PASSWORD', 'SysAdminSas0102' );
     
     /** MySQL hostname */
     define( 'DB_HOST', '10.0.3.200:3306' );
     
     /** Database charset to use in creating database tables. */
     define( 'DB_CHARSET', 'utf8' );
     
     /** The database collate type. Don't change this if in doubt. */
     define( 'DB_COLLATE', '' );
     
     /**#@+
      * Authentication unique keys and salts.
      *
      * Change these to different unique phrases! You can generate these using
      * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
      *
      * You can change these at any point in time to invalidate all existing cookies.
      * This will force all users to have to log in again.
      *
      * @since 2.6.0
      */
     define( 'AUTH_KEY',         'put your unique phrase here' );
     define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
     define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
     define( 'NONCE_KEY',        'put your unique phrase here' );
     define( 'AUTH_SALT',        'put your unique phrase here' );
     define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
     define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
     define( 'NONCE_SALT',       'put your unique phrase here' );
     
     /**#@-*/
     
     /**
      * WordPress database table prefix.
      *
      * You can have multiple installations in one database if you give each
      * a unique prefix. Only numbers, letters, and underscores please!
      */
     $table_prefix = 'wp_';
     
     /**
      * For developers: WordPress debugging mode.
      *
      * Change this to true to enable the display of notices during development.
      * It is strongly recommended that plugin and theme developers use WP_DEBUG
      * in their development environments.
      *
      * For information on other constants that can be used for debugging,
      * visit the documentation.
      *
      * @link https://wordpress.org/support/article/debugging-in-wordpress/
      */
     define( 'WP_DEBUG', false );
     
     /* Add any custom values between this line and the "stop editing" line. */
     
     
     
     /* That's all, stop editing! Happy publishing. */
     
     /** Absolute path to the WordPress directory. */
     if ( ! defined( 'ABSPATH' ) ) {
             define( 'ABSPATH', __DIR__ . '/' );
     }
     
     /** Sets up WordPress vars and included files. */
     require_once ABSPATH . 'wp-settings.php';
     
     ```
   
     
   
   - Masuk ke dalam templates wordpress.conf
   
        ![29](https://user-images.githubusercontent.com/61863147/144443861-7c887fa6-a569-4dd2-8e22-1779d2bdb8d7.PNG)
   
     ```
     server {
          listen 80;
          listen [::]:80;
     
          # Log files for Debugging
          access_log /var/log/nginx/wordpress-access.log;
          error_log /var/log/nginx/wordpress-error.log;
     
          # Webroot Directory for Laravel project
          root /var/www/html/blog;
          index index.php index.html index.htm;
     
          # Your  Name
          server_name lxc_php7.dev;
     
          location / {
                  try_files $uri $uri/ /index.php?$query_string;
          }
     
          # PHP-FPM Configuration Nginx
          location ~ \.php$ {
                  try_files $uri =404;
                  fastcgi_split_path_info ^(.+\.php)(/.+)$;
                  fastcgi_pass unix:/run/php/php7.4-fpm.sock;
                  fastcgi_index index.php;
                  fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
                  include fastcgi_params;
          }
     }
     ```
   
     
   
     - Jalankan ansible kembali untuk menginstall
   
         ![30](https://user-images.githubusercontent.com/61863147/144443991-095f3c89-b039-430a-bf64-23d67a0110dd.PNG)
   
     
   
   - Buka vm.local/blog untuk melakukan checking apakah wordpressnya sudah dapat dijalankan atau belum. Jika sudah dapat dijalankan, maka tampilannya akan berubah menjadi berikut :
   

    	![31](https://user-images.githubusercontent.com/61863147/144444084-53581b08-4d22-4c88-8cb2-3b831c557c93.PNG)

   
      ![33](https://user-images.githubusercontent.com/61863147/144444144-670aee04-5810-4b06-9880-8492f3d8b10e.PNG)


      ![34](https://user-images.githubusercontent.com/61863147/144444175-fa8b4586-a069-46cc-b555-4bd0e239d916.PNG)

     
      ![35](https://user-images.githubusercontent.com/61863147/144444207-9370b548-f8a4-4091-b07e-bb8c83945cc2.PNG)
      
      
      
      ## **Soal  Tambahan**

1.  Laravel

   - Pertama, ganti konfigurasi file lxc_landing 



     ![Picture1](https://user-images.githubusercontent.com/61863147/144595036-60d59a0e-0c52-49fb-8879-1882284b66d5.png)


   * Dan ubah seperti gambar dibawah ini :

     ![Picture2](https://user-images.githubusercontent.com/61863147/144595083-f43e258f-fb01-45a8-98ee-5d4dddccf5e1.png)

   

   * Buatlah ansible soal2


     ![Picture3](https://user-images.githubusercontent.com/61863147/144595097-b2beda75-ad8a-446c-9057-ec4f63b6611b.png)


   * Jalankan ansible

     ![Picture4](https://user-images.githubusercontent.com/61863147/144595221-a5f905f1-6d69-4ba4-91f7-6d8dbeb43b25.png)

     ```markdown
     ---
     - hosts: all
       become : yes
       tasks:
        - name: mengganti php sock
          lineinfile:
           path: /etc/php/7.4/fpm/pool.d/www.conf
           regexp: '^(.*)listen =(.*)$'
           line: 'listen = 127.0.0.1:9001'
           backrefs: yes
        - name: copy the nginx config file 
          copy:
           src: ~/ansible/laravel/lxc_landing.dev
           dest: /etc/nginx/sites-available/lxc_landing.dev
        - name: Symlink lxc_landing.dev
          command: ln -sfn /etc/nginx/sites-available/lxc_landing.dev /etc/nginx/sites-enabled/lxc_landing.dev
          args:
           warn: false
        - name: restart nginx
          service:
           name: nginx
           state: restarted
        - name: restart php7
          service:
           name: php7.4-fpm
           state: restarted
        - name: curl web
          command: curl -i http://lxc_landing.dev
          args:
           warn: false
     © 2021 GitHub, Inc.
     Terms
     Priv
     ```

   ![Picture5](https://user-images.githubusercontent.com/61863147/144595264-ea032743-dc3b-423c-9cee-3fd0849f4ebf.png)


   * Cek dengan cara membuka vm.local. Jika sukses, maka tampilannya akan seperti berikut ini :

   ![Picture6](https://user-images.githubusercontent.com/61863147/144595302-7413e2a0-8382-41c4-97b8-2cb806979d0e.png)

   

2. Wordpress

   - Pada langkah pertama, lakukan hal yang sama seperti langkah pertama pada laravel. Yakni ganti konfigurasi file menjadi wordpress.conf

     ![Picture7](https://user-images.githubusercontent.com/61863147/144595359-420a596d-94ec-4449-b1ef-9f1bbd072ef9.png)


   

   * Dan ubah seperti gambar di bawah ini :

     ![Picture8](https://user-images.githubusercontent.com/61863147/144595376-112112e8-9ff2-4fcd-a71f-77761dc03c55.png)


   

   * Buatlah ansible soal2

     ![Picture9](https://user-images.githubusercontent.com/61863147/144595412-0a81a94b-40da-4f94-bd00-3aa6a72c6701.png)


   

   * Jalankan ansible

     ![Picture10](https://user-images.githubusercontent.com/61863147/144595443-325f4a5a-03bc-4ea7-a485-a823e21d16f8.png)


```
---
- hosts: all
  become : yes
  tasks:
   - name: mengganti php sock
     lineinfile:
      path: /etc/php/7.4/fpm/pool.d/www.conf
      regexp: '^(.*)listen =(.*)$'
      line: 'listen = 127.0.0.1:9001'
      backrefs: yes
   - name: copy the nginx config file 
     copy:
      src: ~/ansible/wordpress/wordpress.conf
      dest: /etc/nginx/sites-available/lxc_php7.dev
   - name: Symlink lxc_php7.dev
     command: ln -sfn /etc/nginx/sites-available/lxc_php7.dev /etc/nginx/sites-enabled/lxc_php7.dev
     args:
      warn: false
   - name: restart nginx
     service:
      name: nginx
      state: restarted
   - name: restart php7
     service:
      name: php7.4-fpm
      state: restarted
   - name: curl web
     command: curl -i http://lxc_php7.dev
     args:
      warn: false
```
![Picture11](https://user-images.githubusercontent.com/61863147/144595495-1843bee4-0380-493b-b9f2-3bfa16597b13.png)



* Cek dengan cara membuka vm.local/blog Jika sukses, maka tampilannya akan seperti berikut ini :

 ![Picture12](https://user-images.githubusercontent.com/61863147/144595541-557a1d80-f359-4291-ac67-d0011f6d7b9f.png)


   
