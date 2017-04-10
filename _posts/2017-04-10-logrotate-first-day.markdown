---
layout:     post
title:      "Logrotate in linux, first day"
subtitle:   "Learn logrotate in linux"
date:       2017-04-10 12:00:00
author:     "Carl Pham"
header-img: "img/logrotate-bg.jpg"
---

<blockquote>Những thông tin theo ý hiểu của bản thân và lượm nhặt về Logrotate. Lưu lại để khi gặp vấn đề có thể giải quyết nhanh chóng.</blockquote>
<h2 class="section-heading">Tổng quan</h2>
<ul>
  <li>Logrotate là một chương trình hỗ trợ việc quản lý các file log trong hệ thống.</li>
  <li>Rotate (xoay vòng) là tiến trình tạo ra 1 file log mới, file log cũ sẽ được xử lý theo các quy định trong cấu hình như: nén/xóa/lưu ở 1 nơi nào đó, ...</li>
  <li>Logrotate hoạt động theo crontab.</li>
</ul>

<h2 class="section-heading">Cài đặt Logrotate</h2>
<p>Logrotate được cài đặt mặc định trong hầu hết các bản phân phối Linux. Có thể kiểm tra bằng lệnh <code># logrotate</code>. Nếu chưa cài đặt thì có thể tiến hành bằng lệnh sau:</p>
<p>
  Trên hệ thống CentOS/Redhat:
  <pre>
  # sudo yum update
  # sudo yum install logrotate
  </pre>
</p>
<p>
  Trên hệ thống Ubuntu/Debian:
  <pre>
  # sudo apt-get update
  # sudo apt-get install logrotate
  </pre>
</p>

<h2 class="section-heading">Cấu hình Logrotate</h2>
<p>Mặc định cấu hình của Logrotate được lưu tại: <code>/etc/logrotate.conf</code>
<pre>
# see "man logrotate" for details
# rotate log files weekly
weekly

# keep 4 weeks worth of backlogs
rotate 4

# create new (empty) log files after rotating old ones
create

# use date as a suffix of the rotated file
dateext

# uncomment this if you want your log files compressed
#compress

# RPM packages drop log rotation information into this directory
include /etc/logrotate.d

# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
    monthly
    create 0664 root utmp
        minsize 1M
    rotate 1
}

/var/log/btmp {
    missingok
    monthly
    create 0600 root utmp
    rotate 1
}

# system-specific logs may be also be configured here.
</pre>
Theo như trên thì file log được rotate hàng tuần (weekly), dữ liệu log được lưu trữ trong vòng 4 file, file log mới sẽ được tạo sau khi rotate file cũ. Các file log được thêm hậu tố ngày trong tên file.
Thông tin cấu hình log file của từng ứng dụng cụ thể được lưu lại trong thư mục <code>/etc/logrotate.d/</code>
Một số cấu hình, tham số Logrotate cơ bản cần biết:</p>

<h3>Rotate theo thời gian</h3>
<p>Có 4 giá trị cấu hình tương ứng với khoảng thời gian file log được rotate:
<ul>
  <li>Daily: Mỗi ngày</li>
  <li>Weekly: Mỗi tuần</li>
  <li>Monthly: Mỗi tháng</li>
  <li>Yearly: Mỗi năm</li>
</ul>
</p>

<h3>Rotate theo dung lượng file</h3>
<p>Ta có thể tùy biến tiến trình rotate dựa vào dung lượng file. Các đơn vị kích thước file có thể sử dụng là: K, M, G
<pre>size 100k
size 100M
size 100G</pre></p>
<p>Lưu ý rằng cấu hình rotate theo dung lượng file luôn được ưu tiên cao hơn theo thời gian.
Ví dụ: log file được cấu hình rotate theo tuần (weekly) và theo dung lượng 100M. Đến giữa tuần file log đạt dung lượng 100M và được rotate. Khi đó Logrotate sẽ bỏ qua rotate ở cuối tuần này và pải đợi sang tuần kế tiếp để thực hiện.</p>

<h3>File log trống (không tồn tại)</h3>
<ul>
  <li><code>missingok</code>: nếu file log vì lý do gì đó mà bị mất hoặc không tồn tại thì Logrotate sẽ tự động di chuyển tới phần cấu hình log của file khác mà không xuất thông báo lỗi</li>
  <li><code>nomissingok</code>: ngược lại của missingok</li>
  <li><code>Notifempty</code>: không rotate nếu file log này trống</li>
</ul>
https://hocvps.com/logrotate/