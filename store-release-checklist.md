# PicPose — checklist kiếm tiền và phát hành

Checklist này là phần cấu hình còn phải làm trong tài khoản Google/Apple. Mã nguồn đã có luồng Free/VIP, Google UMP, banner được gắn nhãn, restore purchase, privacy/terms và chặn release khi thiếu App ID hoặc signing key.

## 1. Play Console — sản phẩm VIP

- Tạo app đúng package `com.picpose.picpose` trước khi đổi package name. Nếu đổi package, đổi cả `applicationId`, namespace, MainActivity package và URL quản lý mua hàng.
- Monetize > Products > In-app products: tạo **one-time product / non-consumable** với ID `picpose_vip_lifetime`.
- Tên gợi ý: `PicPose VIP trọn đời`. Mô tả phải nói rõ: 100 pose, lưu không giới hạn, không quảng cáo, thanh toán một lần, không tự gia hạn.
- Đặt giá hợp lý theo từng thị trường; kích hoạt sản phẩm trước khi test.
- Dùng Internal testing và tester đã khai báo. Cài app từ link test của Play; sideload APK không trả về sản phẩm production đáng tin cậy.
- Tạo license testers để kiểm thử mua, pending, cancel, refund và restore.
- Ghi Review notes: vị trí nút VIP, Product ID, đây là one-time purchase và không cần tài khoản PicPose.

## 2. AdMob và consent

- Tạo Android app trong AdMob, lấy **App ID** (`~`) và một **Banner ad unit ID** (`/`). Không đảo hai loại ID.
- AdMob > Privacy & messaging: tạo/publish European regulations message, chọn ad partners và thêm URL privacy policy công khai.
- Bật US state regulations message nếu phân phối tại thị trường áp dụng.
- Giữ nút **Lựa chọn quyền riêng tư**: UMP tự yêu cầu nó khi khu vực/message cần entry point.
- Chỉ dùng ID test khi debug. Release build cần `PICPOSE_ADMOB_APP_ID`; banner production truyền qua `ADMOB_BANNER_ID`.
- Trong Play Console > App content > Ads, chọn **Yes, my app contains ads** kể cả VIP loại bỏ quảng cáo.
- Không thêm interstitial lúc mở app, khi bấm Back, đang dùng camera hoặc ngay trước/sau thao tác chụp. Banner hiện tại nằm giữa các khu vực nội dung và có nhãn “QUẢNG CÁO”.

## 3. Privacy policy và Data safety

- Host thư mục `docs/` bằng GitHub Pages hoặc dịch vụ HTTPS công khai; trang không được yêu cầu đăng nhập hay cho người xem sửa. Đổi tên đơn vị/liên hệ trong policy nếu tên nhà phát hành trên Play Console không phải “PicPose”.
- URL mặc định của app là `https://recca0201.github.io/picpose-legal/privacy-policy.html`; khai báo cùng URL trong Play Console và AdMob. Chỉ dùng `PRIVACY_POLICY_URL` khi chuyển sang domain khác.
- Kiểm lại Data safety mỗi khi nâng SDK. Với Google Mobile Ads hiện tại, tối thiểu xem xét khai báo dữ liệu được collect/share cho advertising, analytics và fraud prevention:
  - approximate location suy ra từ IP;
  - app interactions;
  - diagnostics;
  - device or other identifiers.
- Dữ liệu truyền bởi Mobile Ads được mã hóa in transit. Không tuyên bố “không thu thập dữ liệu” khi quảng cáo production đang bật.
- Camera/ảnh: PicPose không tự upload ảnh. Khai báo đúng hành vi build thực tế; nếu thêm analytics, cloud sync, crash reporting hoặc backend thì cập nhật policy và Data safety trước khi phát hành.
- App không tạo tài khoản, nên không cần luồng account deletion. Nếu sau này thêm đăng ký tài khoản, phải thêm cả đường dẫn xóa trong app và URL web yêu cầu xóa.

## 4. Billing và bảo mật giao dịch

- Nội dung số/VIP trong bản Play phải thanh toán qua Google Play Billing; không thêm nút chuyển khoản, QR, web checkout hoặc câu chữ dẫn người dùng ra ngoài để mua.
- App chỉ cấp VIP khi nhận trạng thái purchased/restored cùng store receipt, rồi acknowledge/complete transaction.
- Trước quy mô production, nên thêm backend xác minh purchase token bằng Google Play Developer API và xử lý refund/revoke. Cache local hiện giúp dùng offline nhưng không thay thế server verification chống gian lận.
- Test hoàn tiền/thu hồi quyền. Khi có backend, đồng bộ Real-time Developer Notifications để thu hồi entitlement sau refund.
- Nút Restore luôn khả dụng; không yêu cầu đăng nhập PicPose để khôi phục non-consumable.

## 5. Release và nội dung listing

- Tạo upload keystore riêng; không commit `.jks` hoặc `android/key.properties`. Release build không dùng debug key.
- Tăng `version` trong `pubspec.yaml`, build AAB, bật Play App Signing.
- Chạy `flutter analyze`, `flutter test`, `flutter build apk --debug` và internal-test AAB trên ít nhất một máy thật.
- Store listing/screenshots phải thể hiện đúng Free/VIP; không dùng “miễn phí” nếu ảnh/copy làm người dùng tưởng 100 pose đều miễn phí.
- Content rating và Target audience: PicPose không phải app dành riêng cho trẻ em. Nếu chọn trẻ em/unknown age, cần rà lại Families ads requirements.
- App access: không cần account. Review notes giải thích camera có fallback nên reviewer vẫn xem được app nếu emulator không có camera.
- Khai báo privacy policy, Data safety, Ads, Content rating, Target audience, App access, Financial features (nếu form hỏi; app không có), và IAP trước khi Send for review.

## 6. Nếu thêm iOS sau này

- Tạo non-consumable cùng Product ID trong App Store Connect và gắn IAP vào submission.
- Có nút Restore (đã có ở Flutter UI), Privacy Policy và Terms trong app.
- Khai App Privacy cho Mobile Ads; nếu dùng tracking/IDFA, cấu hình ATT/UMP đúng thứ tự và không ép bật tracking để dùng app.
- Thêm iOS AdMob App ID vào `Info.plist`, SKAdNetwork items theo tài liệu SDK và test StoreKit sandbox.
