## Cross-Validation

# Regularization

## Ridge regression
L2 norm
L2 giúp cho các hệ số nhỏ, không được quá lớn
## Lasso regression
Use for feature selection
L1 norm
- L1 giúp cho hầu hết các hệ số bằng 0.  
Thường thì người ta dùng L2 vì đạo hàm đẹp. L1 không có đạo hàm tại 0 và ít được sử dụng hơn. Khi thực hành, có thể chọn L2 trước xem kq thế nào, nếu tệ thì thử sang L1

##  Elastic Net Regression
Khi cả l2l2 và l1l1 regularization được sử dụng, ta có mô hình gọi là [Elastic Net Regression](https://en.wikipedia.org/wiki/Elastic_net_regularization).
