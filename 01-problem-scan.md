# 01 - Problem Scan

## Phase 1 - SCAN: Danh sach bai toan co hoi

| # | Subsidiary | Lens | Mo ta ngan bai toan |
|---|---|---|---|
| 1 | Xanh SM | Ton thoi gian | Dieu phoi vien mat nhieu thoi gian xu ly su co pin yeu cua taxi dien ngoai thuc dia: tra GPS, tim tram sac, soan huong dan va goi ho tro neu xe sap het pin. |
| 2 | Xanh SM | Pain tu nguoi khac | Khach hang huy chuyen vi tai xe den diem don cham, trong khi dieu phoi vien phai doc ghi chu roi dieu chinh thu cong diem don/phuong an don khach. |
| 3 | VinFast | Lap lai | Doi chieu hoa don sac dien doi tac hang tuan voi log sac thuc te, de sai lech do nhieu nguon du lieu va dinh dang file khac nhau. |
| 4 | Vinhomes | Lap lai | Phan loai phan anh cu dan nhu mat nuoc, hong den, on ao, phi dich vu va dieu huong den dung ban quan ly toa nha. |
| 5 | Vinpearl | Pain tu nguoi khac | Tong hop review khach san tu Booking, Agoda, Google Maps de phat hien phan nan khan cap ve phong ban, thai do nhan vien, check-in cham. |
| 6 | Vinmec | Ton thoi gian | Bac si mat nhieu thoi gian soan tom tat ho so xuat vien tu benh an, ket qua xet nghiem va ghi chu dieu tri. |

## De tai duoc chon

**Tro ly AI dieu phoi xu ly su co pin yeu cho doi xe dien Xanh SM**

Ly do chon: bai toan co tac dong van hanh truc tiep, workflow ro, metric co the do bang phut xu ly/SLA, va co ranh gioi an toan de stress-test bang `prompt_prototype.py`.

## Phase 2 - QUICK-ASSESS: 3 Quick Problem Cards

### Quick Problem Card #1 - Xanh SM: Xu ly su co pin yeu ngoai thuc dia

| Truong | Noi dung |
|---|---|
| Bai toan | Dieu phoi vien Xanh SM mat 12-15 phut de xu ly moi su co pin yeu: xac minh vi tri xe, tim tram sac phu hop, soan tin huong dan va quyet dinh co can xe sac di dong hay khong. |
| Cong ty thanh vien | Xanh SM |
| Actor | Dieu phoi vien trung tam van hanh, tai xe taxi dien, doi ho tro sac/cuu ho. |
| Workflow hien tai | 1. Tai xe goi/nhan tin bao pin yeu. 2. Dieu phoi vien tra GPS va % pin. 3. Mo dashboard tram sac de tim tru trong gan nhat. 4. Soan tin chi duong gui tai xe. 5. Goi xe sac di dong neu pin qua thap. |
| Bottleneck | Buoc 3-4 ton 8-10 phut/luot, de sai khi cao diem vi dieu phoi vien phai vua tra ban do vua viet tin tieng Viet ro rang. |
| AI co the ho tro | LLM doc thong tin su co, tao draft huong dan, chon format JSON khi can dispatch xe sac di dong, nhac dieu phoi vien phe duyet truoc khi gui. |
| Metric thanh cong | Giam thoi gian xu ly tu 15 phut xuong duoi 3 phut/luot; 95% draft dung quy tac pin-khoang cach; 100% tin gui ra ngoai co human approval. |
| Quick Architecture | LLM Feature + rule guard cho nguong pin va khoang cach. |

### Quick Problem Card #2 - Vinhomes: Phan loai va dieu huong phan anh cu dan

| Truong | Noi dung |
|---|---|
| Bai toan | Phan anh cua cu dan qua app bi route cham/sai bo phan, lam tang SLA phan hoi va khach hang phai nhac lai nhieu lan. |
| Cong ty thanh vien | Vinhomes |
| Actor | Nhan vien CSKH, ban quan ly toa nha, bo phan ky thuat, cu dan. |
| Workflow hien tai | 1. Cu dan gui phan anh. 2. CSKH doc noi dung. 3. Gan nhan loai su co. 4. Chuyen den bo phan phu trach. 5. Theo doi trang thai xu ly. |
| Bottleneck | Buoc doc va gan nhan ton 3-5 phut/ticket, de nham voi noi dung mo ho hoac co nhieu van de trong mot tin. |
| AI co the ho tro | LLM phan loai y dinh, trich toa nha/can ho/thoi gian, draft cau hoi bo sung khi thieu thong tin. |
| Metric thanh cong | 85% ticket duoc phan loai duoi 10 giay; giam ty le route sai tu 12% xuong duoi 4%. |
| Quick Architecture | LLM Feature ket hop rule router theo taxonomy dich vu. |

### Quick Problem Card #3 - VinFast: Doi chieu hoa don sac dien doi tac

| Truong | Noi dung |
|---|---|
| Bai toan | Doi tai chinh VinFast doi chieu hang nghin dong log sac voi hoa don doi tac, mat nhieu gio va de sot sai lech nho. |
| Cong ty thanh vien | VinFast |
| Actor | Nhan vien tai chinh, doi van hanh tram sac, doi tac cung cap dich vu sac. |
| Workflow hien tai | 1. Nhan file hoa don tu doi tac. 2. Tai log sac noi bo. 3. Chuan hoa cot du lieu. 4. So khop giao dich. 5. Tong hop sai lech can doi soat. |
| Bottleneck | Buoc chuan hoa va giai thich sai lech ton 4-6 gio/tuan, nhieu truong hop can doc ghi chu tu do. |
| AI co the ho tro | Rule-based matching xu ly phan so khop; LLM tom tat ly do sai lech va draft email yeu cau doi tac xac minh. |
| Metric thanh cong | Giam thoi gian doi soat tu 6 gio xuong duoi 1.5 gio/tuan; phat hien 98% giao dich lech so tien/thoi diem. |
| Quick Architecture | Rule + LLM Feature cho giai thich va draft giao tiep. |

## Lua chon cuoi cung

Chon **Card #1 - Xanh SM: Xu ly su co pin yeu ngoai thuc dia** de deep-dive vi day la bai toan co muc do khan cap cao, anh huong truc tiep den tai xe/khach hang, va co ranh gioi van hanh ro rang: AI chi duoc draft, khong tu dong gui; khi pin duoi 5% thi khong duoc de xuat tram sac xa hon 5km.
