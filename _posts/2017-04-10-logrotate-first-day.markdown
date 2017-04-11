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

<h3>Rotate theo số lượng file log</h3>
<pre>rotate [number]</pre>
<p>Quy định số lượng log file cũ đã được giữ lại sau khi rotate. Ví dụ: <code>rotate7</code> giữ lại 7 file log cũ. Trường hợp đã có đủ 7 file log cũ thì file cũ nhất sẽ bị xóa đi để chứa file log mới được tạo.</p>

<h3>Tự động nén Log file</h3>
<p>Tùy chọn <code>Compress</code>: Logrotate sẽ nén tất cả các file log lại sau khi đã được rotate, mặc định bằng gzip.

Nếu bạn muốn sử dụng chương trình nén khác như bzip2, xz hoặc zip thì hãy đặt tên chương trình đó thành biến sau giá trị cấu hình <code>Compresscmd xz</code>

Tham số <code>Delaycompress</code> sẽ hữu dụng trong trường hợp bạn không muốn file log cũ phải nén ngay sau khi vừa được rotate. Thay vào đó, công việc nén sẽ được delay trễ hơn bằng việc sẽ nén file log cũ đó vào lần chạy rotate kế tiếp. Tùy chọn này chỉ hoạt động đi kèm chức năng compress trong file cấu hình, tức bạn phải cấu hình compress trước đó:
<pre>
compress
delaycompress
</pre>
Tùy chọn <code>nocompress</code> không sử dụng tính năng nén đối với file log cũ</p>

<h3>Phân quyền cho Log file</h3>
<p>Chúng ta có thể chỉ định khi logrotate xử lý xong file log cũ sẽ tạo ra file log mới có tên tương ứng để thay thế file log hiện tại. Cấu hình tham số create sẽ quy định việc file log mới tạo ra. Bạn cần đảm bảo đúng phân quyền cho file log mới sau khi rotate.</p>
<p>
Note: Hoạt động tạo file này sẽ chạy trước script postrotate. Ví dụ:
<pre>
create 660 appuser www-data
</pre>
File log mới sẽ có owner là appuser và thuộc group www-data (group mà Apache thường chạy). Quyền hạn 660 cho phép cả owner và user trong cùng 1 nhóm được phép viết, sửa nội dung lên file. Điều này cho phép các ứng dụng PHP viết vào log file.

Nếu bạn không muốn tự động tạo file log mới thì sử dụng option nocreate

Tham số <code>Dateext</code> sẽ giúp cài đặt hậu tố của tên log file là thời gian theo cấu trúc yyyymmdd. Logs file mặc định có các giá trị hậu tố là các con số 1,2,3…
</p>
https://hocvps.com/logrotate/