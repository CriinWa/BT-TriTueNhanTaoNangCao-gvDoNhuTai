# BÁO CÁO: SO SÁNH THUẬT TOÁN TÌM KIẾM QUA ANIMATION

**Notebook:** Maze_Example-Animation_full.ipynb  
**Mục tiêu:** So sánh hành vi các thuật toán tìm kiếm qua trực quan hóa động

**Sinh viên:** [Tên sinh viên] | **Lớp:** [Lớp] | **Ngày:** 4/10/2025

---

## 1. GIỚI THIỆU

### 1.1. Mục đích
So sánh **trực quan** các thuật toán tìm kiếm trong mê cung qua animation để hiểu rõ sự khác biệt về:
- 🎯 **Hành vi khám phá** không gian tìm kiếm
- 📊 **Hiệu quả** (số ô khám phá vs độ dài đường)
- ⚖️ **Trade-off** giữa tối ưu và tốc độ

### 1.2. Thiết lập
- **Mê cung:** open_maze.txt (22×38, không gian mở)
- **Heuristic:** Manhattan distance
- **Animation:** matplotlib.animation.FuncAnimation (cần ffmpeg)

**Ký hiệu màu sắc:**
| 🟦 Start | 🟩 Goal | 🟥 Path | 🟧 Frontier | ⬜ Explored | ⬛ Wall |
|---------|---------|---------|------------|------------|--------|

---

## 2. SO SÁNH TRỰC QUAN CÁC THUẬT TOÁN

### 2.1. Bảng tổng hợp

| Thuật toán | Hình dạng vùng khám phá | Số frames | Độ dài đường | Số ô khám phá | Tối ưu? |
|------------|------------------------|-----------|--------------|---------------|---------|
| **BFS** | 🔵 Sóng tròn, đồng đều | 682 | 54 | 682 | ✅ |
| **DFS** | 🌿 Nhánh dài, quanh co | ~200 | 80-300 | ~150 | ❌ |
| **GBFS** | 🎯 Hẹp, thẳng về đích | ~60 | ~54 | ~60 | ⚠️ |
| **A*** | ⚖️ Hình quả lê cân bằng | ~150 | 54 | ~150 | ✅ |

### 2.2. Animation từng thuật toán

#### 🔵 **BFS - Tìm kiếm mù quáng**
```
Animation: Sóng tròn lan đều từ xuất phát
├─ Frames 1-100:   Mở rộng đồng đều mọi hướng
├─ Frames 100-500: Phủ kín không gian phía trên
└─ Frames 500-682: Cuối cùng đạt đích (54 bước)
```
**Quan sát:**
- ✅ **Tối ưu:** Đảm bảo đường ngắn nhất (54 bước)
- ❌ **Lãng phí:** Khám phá 682 ô (gấp 12.6 lần độ dài đường!)
- 📊 Vùng xám (explored) rất rộng, không có định hướng
- 🎬 Animation mượt, lan tỏa như sóng nước

**Bài học:** "Tối ưu" ≠ "Hiệu quả"

---

#### 🌿 **DFS - Đi sâu không kiểm soát**
```
Animation: Nhánh dài quanh co, backtrack nhiều
├─ Frames 1-50:   Đi sâu theo một hướng
├─ Frames 50-100: Gặp bế tắc, quay lui
└─ Frame N:       Tìm thấy đích sau nhiều vòng (80-300 bước)
```
**Thí nghiệm 100 lần chạy random:**
- Tốt nhất: 126 bước (vẫn gấp **2.3 lần** BFS!)
- Trung bình: ~200 bước
- Tệ nhất: 300 bước (gấp **5.5 lần** BFS!)
- Phạm vi: 126-300 (biến động cực lớn!)

**Quan sát:**
- ❌ **Không tối ưu:** Đường đi rất dài
- ⚠️ **Không ổn định:** Phụ thuộc thứ tự khám phá
- ✅ **Nhanh:** Ít khám phá hơn (nhưng vô ích)
- 🎬 Animation "nhảy nhót", khó dự đoán

**Bài học:** "Nhanh" ≠ "Tốt"

---

#### 🎯 **GBFS - Tham lam hướng đích**
```
Animation: Đi thẳng về đích, vùng hẹp
├─ Frames 1-20:  Nhảy thẳng về phía goal
├─ Frames 20-40: Vòng qua vật cản (nếu có)
└─ Frames ~60:   Đạt đích nhanh
```
**Quan sát:**
- ⚡ **Hiệu quả nhất:** Chỉ ~60 ô khám phá
- 🎯 **Có định hướng:** Frontier rất hẹp, tập trung
- ⚠️ **Không đảm bảo:** Có thể bỏ sót đường tối ưu
- 🎬 Animation nhanh, đi thẳng

**Bài học:** Heuristic tốt → hiệu quả cao, nhưng không chắc tối ưu

---

#### ⭐ **A\* - Cân bằng hoàn hảo**
```
Animation: Hình quả lê, mở rộng có chọn lọc
├─ Frames 1-50:   Vùng khám phá dần hình quả lê
├─ Frames 50-100: Hướng về đích nhưng cẩn thận
└─ Frames ~150:   Đạt đích với đường tối ưu (54 bước)
```
**Quan sát:**
- ✅ **Tối ưu:** Đường ngắn nhất như BFS (54 bước)
- ✅ **Hiệu quả:** Chỉ ~150 ô (giảm 78% so với BFS!)
- ⚖️ **Cân bằng:** f(n) = g(n) + h(n)
- 🎬 Animation đẹp nhất, hình quả lê đặc trưng

**Bài học:** Kết hợp thông tin → tối ưu cả 2 mặt

---

### 2.3. Weighted A* - Điều chỉnh hành vi

**f(n) = g(n) + W × h(n)**

| W | Hành vi | Vùng khám phá | Kết quả |
|---|---------|---------------|---------|
| **0.1** | → BFS | 🔵 Rộng, tròn | Tối ưu, chậm |
| **1.0** | A* chuẩn | ⚖️ Quả lê | Tối ưu, cân bằng ⭐ |
| **2.0** | → GBFS | 🎯 Hẹp | Không chắc, nhanh |
| **1000** | ≈ GBFS | ➡️ Rất hẹp | Không tối ưu |

**Insight:** W điều khiển trade-off giữa khám phá (BFS) và khai thác (GBFS)

---

## 3. PHÂN TÍCH SO SÁNH

### 3.1. Hình dạng vùng khám phá

```
       BFS                GBFS               A*
        ⬜                  ⬜                ⬜
      ⬜⬜⬜              ⬜🟧⬜            ⬜🟧⬜
    ⬜⬜🟧⬜⬜              🟧                ⬜🟧⬜
  ⬜⬜⬜🟧⬜⬜⬜            🟧              ⬜⬜🟧⬜⬜
    ⬜⬜🟦⬜⬜              🟦                ⬜🟦⬜
      (Tròn)            (Thẳng)          (Quả lê)
```

### 3.2. So sánh chi tiết

**BFS vs A*:**
- Cùng tối ưu (54 bước)
- BFS khám phá 682 ô | A* chỉ 150 ô
- **A* hiệu quả hơn 78%!**

**GBFS vs A*:**
- GBFS nhanh hơn (60 vs 150 ô)
- Nhưng GBFS không đảm bảo tối ưu
- **A* đáng tin hơn**

**DFS vs tất cả:**
- DFS nhanh nhất về thời gian
- Nhưng đường đi dài nhất (80-300 bước)
- **Không nên dùng cho pathfinding**

### 3.3. Timeline animation

| Thời điểm | BFS | DFS | GBFS | A* |
|-----------|-----|-----|------|-----|
| **Frame 50** | Sóng nhỏ | Đi sâu 1 nhánh | Gần đích | Quả lê nhỏ |
| **Frame 100** | Sóng lớn | Backtrack | ✅ Xong! | Hướng đích |
| **Frame 150** | Lan rộng | Thử nhánh khác | - | ✅ Xong! |
| **Frame 682** | ✅ Xong! | Vẫn đang tìm | - | - |

**Kết luận:** GBFS nhanh nhất, A* cân bằng, BFS chậm nhưng chắc chắn

---

## 4. INSIGHTS QUAN TRỌNG

### 4.1. Từ Animation học được gì?

**👁️ Thấy được (không thể học từ code):**

1. **BFS lãng phí thế nào:**
   - Vùng xám rộng khắp không gian
   - Khám phá cả vùng xa đích vô nghĩa
   - Giải thích tại sao tốn bộ nhớ O(b^d)

2. **DFS bất ổn ra sao:**
   - Đường đi quanh co, zigzag
   - Thay đổi thứ tự → kết quả khác hẳn
   - Hiểu tại sao không dùng cho tìm đường ngắn

3. **GBFS "mù quáng tham lam":**
   - Chỉ nhìn về đích, bỏ qua chi phí đã đi
   - May mắn → tốt, không may → tệ
   - Phụ thuộc hoàn toàn vào heuristic

4. **A* "thông minh cân bằng":**
   - Hình quả lê: Rộng gần xuất phát, hẹp gần đích
   - Vừa khám phá đủ, vừa khai thác hiệu quả
   - Đây là lý do A* là "gold standard"

### 4.2. Nguyên lý hoạt động

**BFS:** "Tìm mọi thứ ở xa d trước khi đến d+1"
- → Đồng đều nhưng lãng phí

**DFS:** "Đi sâu tới cùng rồi quay lui"
- → Nhanh nhưng không thông minh

**GBFS:** "Chọn ô gần đích nhất"
- → Tham lam nhưng rủi ro

**A*:** "Chọn ô có tổng (đã đi + còn lại) nhỏ nhất"
- → Cân bằng và tối ưu ⭐

---

## 5. ỨNG DỤNG & KHUYẾN NGHỊ

### 5.1. Khi nào dùng thuật toán nào?

**🏆 A* - Lựa chọn mặc định:**
- ✅ Có heuristic tốt
- ✅ Cần đường tối ưu
- ✅ Cần hiệu quả
- → **Game pathfinding, robot navigation, GPS**

**🔵 BFS - Khi không có heuristic:**
- ✅ Đảm bảo tối ưu tuyệt đối
- ❌ Chấp nhận tốn bộ nhớ
- → **Puzzle solving, social network (shortest path)**

**🎯 GBFS - Khi cần nhanh:**
- ⚠️ Chấp nhận không tối ưu
- ✅ Heuristic rất tốt
- → **Real-time game AI, quick approximation**

**🌿 DFS - Chỉ khi:**
- ❌ KHÔNG quan tâm độ dài đường
- ✅ Chỉ cần tìm 1 nghiệm bất kỳ
- → **Maze generation, backtracking problems**

### 5.2. Giá trị của Animation

**✅ Nên xem Animation khi:**
- Học thuật toán lần đầu
- Giảng dạy, thuyết trình
- Debug thuật toán mới
- So sánh hành vi trực quan

**❌ KHÔNG dùng Animation khi:**
- Benchmark hiệu suất (chậm)
- Production code (overhead)
- Mê cung lớn (quá nhiều frame)

### 5.3. Workflow đề xuất

```
1. Xem Animation (notebook này)
   → Hiểu cách thuật toán hoạt động
   
2. Đọc báo cáo so sánh (Maze_Example.ipynb)
   → Đo lường hiệu suất chính xác
   
3. Kết hợp
   → Kết luận toàn diện
```

---

## 6. KẾT LUẬN

### 6.1. Tóm tắt

**Qua animation, chúng ta thấy rõ:**

| Thuật toán | Đặc trưng animation | Đánh giá |
|------------|-------------------|----------|
| BFS | 🔵 Sóng tròn đều | Tối ưu nhưng lãng phí |
| DFS | 🌿 Nhánh quanh co | Nhanh nhưng kém |
| GBFS | 🎯 Thẳng về đích | Hiệu quả nhưng rủi ro |
| **A*** | ⚖️ **Quả lê cân bằng** | **Tối ưu + Hiệu quả ⭐** |

### 6.2. Thông điệp chính

1. **A* là lựa chọn tốt nhất** cho pathfinding
   - Kết hợp ưu điểm BFS (tối ưu) + GBFS (hiệu quả)
   - Hình quả lê là "chữ ký" của thuật toán thông minh

2. **Heuristic quyết định hiệu quả**
   - Manhattan distance tốt cho grid 2D
   - Heuristic tốt → GBFS và A* hiệu quả
   - Heuristic kém → trở thành BFS

3. **Animation là công cụ học tập mạnh**
   - Chuyển trừu tượng → trực quan
   - Hiểu sâu hơn code
   - Nhớ lâu hơn lý thuyết

### 6.3. Con số ấn tượng

```
BFS:  54 bước, 682 ô    → Tỉ lệ 1:12.6
A*:   54 bước, 150 ô    → Tỉ lệ 1:2.8  ⭐
GBFS: 54 bước, 60 ô     → Tỉ lệ 1:1.1  (may mắn)
DFS:  126-300 bước      → Không thể chấp nhận

Kết luận: A* hiệu quả hơn BFS 78% về không gian khám phá!
```

---

## PHỤ LỤC

### A. Vấn đề kỹ thuật

**Lỗi thường gặp:**
```python
RuntimeError: Requested MovieWriter (ffmpeg) not available
```

**Giải quyết:**
```bash
# Windows
choco install ffmpeg

# Linux/Mac
sudo apt-get install ffmpeg  # hoặc brew install ffmpeg
```

**Alternative (không cần ffmpeg):**
```python
rc('animation', html='jshtml')  # Thay vì 'html5'
```

### B. Tham số quan trọng

```python
# Bật animation
result = best_first_search(maze, strategy="A*", 
                          animation=True,  # Lưu trạng thái
                          vis=False,       # Không print
                          debug=False)     # Không debug

# Tạo video
animate_maze(result, repeat=False)
```

### C. File liên quan

- `Maze_Example.ipynb` - So sánh hiệu suất (không animation)
- `Maze_Example-Animation_full.ipynb` - Notebook này
- `tree_search_solution.py` - Implementation
- `maze_helper.py` - Helper functions

---

**📌 Ghi chú:** Báo cáo tập trung vào **so sánh trực quan** qua animation. Để có số liệu hiệu suất chính xác, xem `Maze_Example.ipynb`.
