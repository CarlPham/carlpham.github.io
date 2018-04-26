---
layout:     post
title:      "Install Nodejs on Centos7"
subtitle:   "Install Nodejs on Centos 7"
date:       2018-04-26 9:30:00
author:     "Carl Pham"
header-img: "img/nodejs"
---
<blockquote>Cài đặt Nodejs phiên bản mới nhất trên Centos7. Nói chung là có rất nhiều cách để cài đặt nhưng đây là cách mà mình hay làm. Note lại ở đây để có lúc quên thì vào coi lại ^.^</blockquote>
<h2 class="section-heading">Hướng dẫn cài đặt Nodejs trên Centos 7</h2>
<ul>
  <li>Định nghĩa Nodejs là gì thì mình ko nhắc lại nữa cho lan man. - <a href="https://nodejs.org/en/">Trang chủ</a></li>
  <li>CentOS là một bản phân phối hệ điều hành miễn phí dựa trên Linux kernel. Nó có nguồn gốc từ Red Hat Enterprise Linux (RHEL).</li>
</ul>

<h2 class="section-heading">Step 0</h2>
<p>
  Bản Nodejs mới nhất được lưu tại: http://nodejs.org/dist/latest/
  Mình vào đây chọn gói tương ứng với hệ điều hành phù hợp và copy link. Cụ thể ở đây mình chọn gói <pre>node-v10.0.0-linux-x64.tar.gz</pre> là gói mới nhất ở thời điểm hiện tại.
  Lưu ý: mình chọn linux-x64 là vì mình dùng hệ điều hành Linux (Centos 7) và 64 bit
</p>

<h2 class="section-heading">Step 1</h2>
<p>
  Download về server:
  <pre> cd ~ </pre>
  <pre> wget http://nodejs.org/dist/latest/node-v10.0.0-linux-x64.tar.gz </pre>
</p>

<h2 class="section-heading">Step 2</h2>
<p>
  Giải nén và cài đặt trong 1 nốt nhạc.
  <pre>
  sudo tar --strip-components 1 -xzvf node-v* -C /usr/local
  </pre>
  Giải thích:
    - Câu lệnh trên thực thi giải nén file node-v10.0.0-linux-x64.tar.gz ra sẽ có các files: CHANGELOG.md, LICENSE, README.md và các thư mục bin, include, lib, share.
    - Tiếp theo nó sẽ move hết tất cả các file và thư mục đã giải nén vào thư mục /usr/local
</p>

<h2 class="section-heading">Step 3</h2>
<p>
  OK, tới đây là bạn đã cài đặt xong nodejs rồi đó. Thử check lại bằng lệnh sau:
  <pre>
  node --version
  </pre>
  Kết quả trả về là: v10.0.0
</p>
