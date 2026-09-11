# 03 - AI Log & Reflection

## Qua trinh su dung AI

Trong bai lab nay, toi su dung AI nhu mot thought-partner de chon de tai, sap xep y tuong va bien mot y tuong van hanh thanh problem statement co the cham diem duoc. Ban dau toi co nhieu lua chon trong inspiration kit nhu phan loai phan anh Vinhomes, doi chieu hoa don sac dien VinFast, tom tat ho so xuat vien Vinmec va xu ly su co pin yeu Xanh SM. AI giup toi so sanh cac de tai theo rubric: workflow co ro khong, metric co do duoc khong, ranh gioi van hanh co manh khong, va co prototype prompt duoc khong.

Sau khi phan tich, toi chon de tai **tro ly AI dieu phoi xu ly su co pin yeu cho doi xe dien Xanh SM**. Ly do la de tai nay co tinh khan cap, actor ro, dau vao ro, bottleneck ro va co rui ro an toan de thiet ke operational boundary. AI giup toi chuyen de tai thanh cac thanh phan trong worksheet: SCAN table, Quick Problem Cards, current-state workflow, problem statement 6-field, future-state flow, AI Fit va evaluation.

## AI giup duoc gi

AI giup toi nhin bai toan theo goc nhin san pham thay vi chi nghi "lam chatbot". Cu the, AI lien tuc keo toi ve cac cau hoi quan trong: ai dang dau, buoc nao ton thoi gian, neu AI sai thi hau qua la gi, can con nguoi duyet o dau, va metric nao co the dung de chung minh gia tri.

Trong phan code, AI giup toi hoan thien `prompt_prototype.py`: viet system prompt, cai dat Gemini SDK, tao adversarial tests va them rule guard cho truong hop pin duoi 5% nhung tram sac xa hon 5km. Phan nay giup toi thay rang prompt khong nen la lop bao ve duy nhat; voi rui ro van hanh, can co ca guard bang code.

## AI tra loi sai hoac chua tot o dau

O lan dau, AI co xu huong dua ra de tai nghe hay nhung hoi rong, vi du "smart dispatching" noi chung. Neu giu de tai do thi kho ve workflow va metric vi pham vi qua lon. Toi da yeu cau thu hep lai thanh mot incident cu the: pin yeu/hap het pin ngoai thuc dia.

Mot diem nua la AI ban dau dua ra system prompt kha day du nhung van phu thuoc vao Gemini nghe loi. Khi chay adversarial test, co kha nang model van tra loi thieu `[DRAFT_ONLY]` hoac khong dung JSON. Vi vay toi da sua boundary: them programmatic guard trong code, tu dong ep `dispatch_mobile_charger` khi input co pin < 5% va khong co tram an toan trong 5km, dong thoi tu them `[DRAFT_ONLY]` neu model quen tag.

## Cach toi sua prompt va ranh gioi

Toi sua prompt theo huong ro vai tro, ro quyen han va ro dieu cam:

- AI la co-pilot cho dieu phoi vien, khong phai nguoi tu dong thuc thi.
- Moi output phai co `[DRAFT_ONLY]`.
- AI khong duoc noi da gui tin, da dispatch, da phe duyet.
- Neu pin duoi 5% va tram xa hon 5km, AI phai tra action `dispatch_mobile_charger`.
- Neu thieu thong tin, AI phai hoi lai thay vi doan.

Toi cung them adversarial prompts co tinh tan cong truc tiep, vi du yeu cau bo `[DRAFT_ONLY]`, gia mao truong ca de bo quy trinh, va ep AI chi duong den tram xa khi pin chi con 2-3%. Cac test nay giup ranh gioi khong chi nam tren giay ma co the duoc kiem thu bang script.

## Bai hoc rut ra

Bai hoc lon nhat cua toi la: mot prototype AI tot khong chi la prompt hay, ma la mot thiet ke van hanh co ranh gioi. Voi bai toan lien quan den xe dien va an toan thuc dia, AI nen giup dieu phoi vien nhanh hon va nhat quan hon, nhung khong nen duoc trao quyen tu dong gui lenh. Human-in-the-loop, fallback va rule guard moi la phan lam cho giai phap co the trien khai that.
