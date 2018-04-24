---
layout:     post
title:      "Let's Encrypt and Vestacp on Centos7"
subtitle:   "Install Let's Encrypt and VestaCP on Centos 7"
date:       2018-04-24 9:30:00
author:     "Carl Pham"
header-img: "img/how-to-install-lets-encrypt-using-vesta-control-panel"
---
<blockquote>Ghi chú của bản thân về Let'sEncrypt và VestaCP trong quá trình làm việc và học tập.</blockquote>
<h2 class="section-heading">Hướng dẫn cài đặt Let's Encrypt và VestaCP trên Centos 7</h2>
<ul>
  <li>VestaCP là một panel quản lý miễn phí và mạnh mẽ. Được phát triển trên ngôn ngữ PHP. - <a href="https://vestacp.com">Trang chủ</a></li>
  <li>Let's Encrypt là nhà cung cấp chứng chỉ SSL miễn phí, tự động, hoạt động vì lợi ích của cộng đồng. Nó được quản lý bởi Internet Security Research Group (ISRG).</li>
  <li>CentOS là một bản phân phối hệ điều hành miễn phí dựa trên Linux kernel. Nó có nguồn gốc từ Red Hat Enterprise Linux (RHEL).</li>
</ul>

<h2 class="section-heading">Step 0</h2>
<p>
  Cài đặt Vestacp theo hướng dẫn trên trang chủ
</p>

<h2 class="section-heading">Step 1</h2>
<p>
  Login vào server bằng ssh sau đó di chuyển vào thư mục /usr/local
  <pre> cd /usr/local </pre>
</p>

<h2 class="section-heading">Step 2</h2>
<p>
  Clone Letsencrypt and tool, sẽ tự động tạo 2 thư mục /usr/local/letsencrypt and /usr/local/letsencrypt-vesta
  <pre>
  git clone https://github.com/letsencrypt/letsencrypt.git
  git clone https://github.com/interbrite/letsencrypt-vesta.git
  </pre>
  Note: có thể sẽ bị báo lỗi chưa cài git, khi đó dùng yum cài đặt git như sau: <pre>yum install git -y</pre>
</p>

<h2 class="section-heading">Step 3</h2>
<p>
  Tạo thư mục "webroot" nơi mà Letsencrpyt dùng để ghi các file cần thiết khi verification domain
  <pre>mkdir -p /etc/letsencrypt/webroot</pre>
</p>

<h2 class="section-heading">Step 4</h2>
<p>
  Symlink file cấu hình Apache vào trong thư mục conf.d của Apache. Điều này cho phép Apache phục vụ đúng các tệp xác thực từ thư mục webroot ở trên
  <pre>ln -s /usr/local/letsencrypt-vesta/letsencrypt.conf /etc/httpd/conf.d/letsencrypt.conf</pre>
</p>

<h2 class="section-heading">Step 5</h2>
<p>
  Symlink letsencrypt-auto và letsencrypt-vesta vào /usr/local/bin để dễ dàng truy cập
  <pre>
  ln -s /usr/local/letsencrypt/letsencrypt-auto /usr/local/bin/letsencrypt-auto
  ln -s /usr/local/letsencrypt-vesta/letsencrypt-vesta /usr/local/bin/letsencrypt-vesta</pre>
</p>

<h2 class="section-heading">Step 6</h2>
<p>
  Restart webserver:
  <pre>service httpd restart</pre>
</p>

<h2 class="section-heading">Step 7</h2>
<p>
  Sử dụng lệnh để cài đặt Letsencrypt SSL cho domain:
  <pre>letsencrypt-vesta username domain</pre>
  chi tiết hơn: <pre>sudo letsencrypt-vesta [-m email] [-u] user1 [domainlist1] [...-u userN [domainlistN]]</pre>
</p>

<h2 class="section-heading">Tham khảo</h2>
<p>
  - <a href="https://github.com/interbrite/letsencrypt-vesta"> Let'sEncrypt VestaCP </a>
</p>

<h2 class="section-heading">Tip</h2>
<p>
  Sau khi cài đặt xong đến Step 7 ở trên có thể gặp lỗi khi chạy install Let's Encrypt:
  <pre>/usr/local/bin/letsencrypt-vesta: line 226: /usr/local/certbot/certbot-auto: No such file or directory</pre>
Let's Encrypt returned an error status.  Aborting.
=> Khi đó cần cài đặt thêm certbot-auto như sau:
  <pre>
  cd /usr/local
  git clone https://github.com/certbot/certbot.git
  </pre>
Sau đó chạy lại step 8, nó sẽ yêu cầu cài thêm các soft phụ thuộc, khi đó cứ chọn Yes thôi.
Tiếp theo cần chạy certbot-auto dành riêng cho nginx bằng lệnh sau:
  <pre>/usr/local/certbot/certbot-auto --nginx</pre>
Tới đây là ngon rồi, nó sẽ hỏi tên domain cần cài đặt, chỉ việc chọn theo số tương ứng là xong.
Nó có cho mình 2 option để chọn, mình chọn đại số 1 vì chưa bít nó khác nhau như thế nào:
1: No redirect - Make no further changes to the webserver configuration.
2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
new sites, or if you're confident your site works on HTTPS. You can undo this
change by editing your web server's configuration.
</p>

<h2 class="section-heading">Link tham khảo thêm về Certbot</h2>
<p>
  - <a href="https://certbot.eff.org/lets-encrypt/debianwheezy-nginx">Certbot</a>
</p>
