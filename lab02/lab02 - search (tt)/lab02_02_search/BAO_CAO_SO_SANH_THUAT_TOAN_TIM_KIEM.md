# BÁO CÁO SO SÁNH CÁC THUẬT TOÁN TÌM KIẾM TRONG MÊ CUNG

**Yêu cầu:** Mazes: Compare search behavior (no animation)

**Sinh viên thực hiện:** [Tên sinh viên]  
**Lớp:** [Lớp]  
**Ngày:** 4 tháng 10, 2025

---

## 1. GIỚI THIỆU

### 1.1. Mục tiêu
Bài thực hành này nhằm so sánh hành vi tìm kiếm của các thuật toán khác nhau trong việc giải quyết bài toán tìm đường trong mê cung (maze), bao gồm:
- **BFS** (Breadth-First Search) - Tìm kiếm theo chiều rộng
- **DFS** (Depth-First Search) - Tìm kiếm theo chiều sâu
- **GBFS** (Greedy Best-First Search) - Tìm kiếm tham lam
- **A*** (A-Star Search) - Thuật toán A*
- **IDS** (Iterative Deepening Search) - Tìm kiếm lặp tăng dần độ sâu

### 1.2. Mê cung sử dụng
Thực nghiệm được thực hiện trên mê cung **L_maze.txt** với:
- Điểm xuất phát (S): Tọa độ (6, 9)
- Điểm đích (G): Tọa độ (13, 2)
- Các vật cản (tường) màu đen
- Không gian di chuyển màu xám/trắng

### 1.3. Hàm Heuristic
Sử dụng **Manhattan Distance** làm hàm heuristic:
```python
h(n) = |x1 - x2| + |y1 - y2|
```

---

## 2. PHÂN TÍCH CHI TIẾT CÁC THUẬT TOÁN

### 2.1. BFS (Breadth-First Search)

**Nguyên lý:**
- Khám phá tất cả các node ở độ sâu k trước khi chuyển sang độ sâu k+1
- Sử dụng cấu trúc Queue (FIFO - First In First Out)
- Đảm bảo tìm được đường đi ngắn nhất

**Kết quả thực nghiệm (Thứ tự khám phá: NESW):**
- ⏱️ **Thời gian thực thi:** 3.5 ms
- 📏 **Độ dài đường đi:** 16 bước
- 🔍 **Số ô đã khám phá:** 151 ô
- ✅ **Tính tối ưu:** Tối ưu (đường đi ngắn nhất)

**Đặc điểm:**
- ✅ **Ưu điểm:** 
  - Đảm bảo tìm được đường đi ngắn nhất
  - Hoàn chỉnh (luôn tìm được nghiệm nếu có)
- ❌ **Nhược điểm:**
  - Khám phá nhiều ô không cần thiết (151 ô cho đường đi 16 bước)
  - Tiêu tốn bộ nhớ cao với không gian rộng

**Hành vi tìm kiếm:**
- Mở rộng đồng đều theo tất cả hướng từ điểm xuất phát
- Khám phá các vùng xung quanh một cách có hệ thống
- Màu xám cho thấy phạm vi khám phá rất rộng

---

### 2.2. DFS (Depth-First Search)

**Nguyên lý:**
- Đi sâu vào một nhánh cho đến khi gặp bế tắc hoặc đích
- Sử dụng cấu trúc Stack (LIFO - Last In First Out)
- Không đảm bảo tìm được đường đi ngắn nhất

**Kết quả thực nghiệm (Thứ tự khám phá: NESW):**
- ⏱️ **Thời gian thực thi:** 1.18 ms (nhanh nhất!)
- 📏 **Độ dài đường đi:** 80 bước (dài hơn BFS nhiều!)
- 🔍 **Số ô đã khám phá:** 149 ô
- ✅ **Tính tối ưu:** Không tối ưu

**Đặc điểm:**
- ✅ **Ưu điểm:**
  - Thời gian chạy nhanh nhất
  - Tiết kiệm bộ nhớ hơn BFS trong một số trường hợp
- ❌ **Nhược điểm:**
  - Đường đi tìm được rất dài (80 bước so với 16 bước của BFS)
  - Phụ thuộc nhiều vào thứ tự khám phá các hướng
  - Có thể đi vào đường vòng không hiệu quả

**Hành vi tìm kiếm:**
- Đi theo một hướng sâu trước khi quay lui
- Tạo ra đường đi quanh co, không hiệu quả
- Đường màu đỏ cho thấy nhiều đoạn đi vòng không cần thiết

**Lưu ý về độ phức tạp không gian:**
- Implementation này giữ toàn bộ cây tìm kiếm trong bộ nhớ (cấu trúc "reached")
- Độ phức tạp không gian: O(b^m) - tệ hơn BFS!
- DFS không có cấu trúc "reached" có độ phức tạp O(bm) nhưng có thể bị vòng lặp vô hạn

---

### 2.3. GBFS (Greedy Best-First Search)

**Nguyên lý:**
- Chọn node có giá trị heuristic h(n) nhỏ nhất (gần đích nhất)
- Tham lam - chỉ xét khoảng cách đến đích, không quan tâm đường đã đi
- Không đảm bảo tìm được đường đi tối ưu

**Kết quả thực nghiệm (Thứ tự khám phá: NESW):**
- ⏱️ **Thời gian thực thi:** 2.62 ms
- 📏 **Độ dài đường đi:** 16 bước (bằng BFS!)
- 🔍 **Số ô đã khám phá:** 61 ô (ít nhất!)
- ✅ **Tính tối ưu:** May mắn tìm được đường tối ưu trong trường hợp này

**Đặc điểm:**
- ✅ **Ưu điểm:**
  - Khám phá ít ô nhất (61 ô)
  - Tập trung vào hướng đích
  - Hiệu quả cao trong mê cung đơn giản
- ❌ **Nhược điểm:**
  - Không đảm bảo tối ưu trong tất cả trường hợp
  - Có thể bị "lừa" bởi heuristic không chính xác

**Hành vi tìm kiếm:**
- Tập trung khám phá theo hướng đích
- Vùng màu cam rất hẹp, cho thấy tính định hướng cao
- Đường đi trực tiếp, ít quanh co

---

### 2.4. A* (A-Star Search)

**Nguyên lý:**
- Kết hợp chi phí đã đi g(n) và ước lượng chi phí còn lại h(n)
- Đánh giá node theo f(n) = g(n) + h(n)
- Đảm bảo tối ưu nếu heuristic admissible (h(n) ≤ chi phí thực tế)

**Kết quả thực nghiệm (Thứ tự khám phá: NESW):**
- ⏱️ **Thời gian thực thi:** 1.64 ms
- 📏 **Độ dài đường đi:** 16 bước (tối ưu!)
- 🔍 **Số ô đã khám phá:** 66 ô
- ✅ **Tính tối ưu:** Tối ưu (đảm bảo)

**Đặc điểm:**
- ✅ **Ưu điểm:**
  - Đảm bảo tìm được đường đi ngắn nhất
  - Hiệu quả hơn BFS (chỉ 66 ô so với 151 ô)
  - Cân bằng giữa tối ưu và hiệu suất
- ❌ **Nhược điểm:**
  - Phức tạp hơn BFS và DFS
  - Phụ thuộc vào chất lượng heuristic

**Hành vi tìm kiếm:**
- Cân bằng giữa chi phí đã đi và ước lượng còn lại
- Khám phá có định hướng nhưng vẫn đảm bảo tối ưu
- Vùng khám phá hẹp hơn BFS nhưng rộng hơn GBFS một chút

---

## 3. SO SÁNH TỔNG HỢP

### 3.1. Bảng so sánh các chỉ số

| Thuật toán | Thời gian (ms) | Độ dài đường | Số ô khám phá | Tối ưu? | Hoàn chỉnh? |
|------------|---------------|--------------|---------------|---------|-------------|
| **BFS**    | 3.5           | 16           | 151           | ✅ Có   | ✅ Có       |
| **DFS**    | 1.18          | 80           | 149           | ❌ Không| ✅ Có       |
| **GBFS**   | 2.62          | 16           | 61            | ⚠️ Không đảm bảo | ✅ Có |
| **A***     | 1.64          | 16           | 66            | ✅ Có   | ✅ Có       |

### 3.2. Bảng đo thời gian chi tiết (medium maze, 50 lần chạy)

| Thuật toán      | Thời gian trung bình (ms) |
|-----------------|---------------------------|
| BFS             | 2                         |
| DFS             | 1                         |
| GBFS            | 2                         |
| A*              | 2                         |
| DFS(no reached) | 2                         |

### 3.3. Phân tích hiệu suất

**🏆 Thuật toán tốt nhất: A***
- Tìm được đường đi tối ưu (16 bước)
- Hiệu quả cao (chỉ 66 ô khám phá)
- Thời gian nhanh (1.64 ms)
- Đảm bảo tính hoàn chỉnh và tối ưu

**⚡ Nhanh nhất: DFS**
- Thời gian thực thi: 1.18 ms
- Nhưng đường đi rất dài (80 bước - gấp 5 lần tối ưu!)
- Không phù hợp khi cần đường đi ngắn

**🎯 Hiệu quả nhất: GBFS**
- Khám phá ít ô nhất (61 ô)
- May mắn tìm được đường tối ưu trong trường hợp này
- Nhưng không đảm bảo tối ưu trong mọi trường hợp

**📊 Chuẩn xác nhất: BFS**
- Luôn tìm được đường ngắn nhất
- Nhưng tốn kém về không gian (151 ô)
- Phù hợp khi không có heuristic tốt

---

## 4. ẢNH HƯỞNG CỦA THỨ TỰ KHÁM PHÁ

### 4.1. Thứ tự NESW vs SENW vs WSEN vs Random

Thí nghiệm cho thấy thứ tự khám phá các hướng (North, East, South, West) có ảnh hưởng lớn đến:

**Với DFS:**
- Thứ tự khác nhau → Đường đi hoàn toàn khác nhau
- Độ dài đường đi có thể thay đổi đáng kể
- Cần chạy nhiều lần với random và chọn kết quả tốt nhất

**Với BFS và A*:**
- Thứ tự ảnh hưởng ít hơn
- Vẫn tìm được đường tối ưu
- Có thể ảnh hưởng đến thời gian thực thi

### 4.2. Random DFS - Chạy nhiều lần

Notebook có thử nghiệm chạy DFS random 100 lần và chọn kết quả tốt nhất:
- Có thể tìm được đường đi tốt hơn
- Tốn thời gian nhưng không đảm bảo tối ưu
- **IDS** (Iterative Deepening Search) là giải pháp tốt hơn

---

## 5. BIẾN THỂ CỦA A*: WEIGHTED A*

### 5.1. Công thức
```
f(n) = g(n) + W × h(n)
```

### 5.2. Ảnh hưởng của trọng số W

**W > 1:** Xu hướng về GBFS
- W càng lớn → càng tham lam
- Không đảm bảo tối ưu
- Có thể nhanh hơn trong một số trường hợp

**W = 1:** A* chuẩn
- Cân bằng tối ưu
- Đảm bảo tìm đường ngắn nhất

**W < 1:** Xu hướng về BFS/UCS
- W càng nhỏ → càng giống BFS
- Đảm bảo tối ưu
- Có thể chậm hơn

---

## 6. CÁC THUẬT TOÁN KHÁC

### 6.1. IDS (Iterative Deepening Search)

**Nguyên lý:**
- Chạy DFS với giới hạn độ sâu tăng dần
- Kết hợp ưu điểm của BFS (tối ưu) và DFS (tiết kiệm bộ nhớ)

**Đặc điểm:**
- Độ phức tạp không gian: O(bd)
- Độ phức tạp thời gian: O(b^d)
- Đảm bảo tối ưu và hoàn chỉnh

### 6.2. Depth-Limited DFS

**Nguyên lý:**
- DFS với giới hạn độ sâu cố định
- Tránh đi quá sâu vào các nhánh không hiệu quả

**Vấn đề:**
- Nếu giới hạn quá nhỏ → không tìm được nghiệm
- Nếu giới hạn quá lớn → giống DFS thông thường

---

## 7. KẾT LUẬN VÀ KHUYẾN NGHỊ

### 7.1. Kết luận chính

1. **A* là thuật toán cân bằng nhất** cho bài toán tìm đường trong mê cung:
   - Đảm bảo tối ưu
   - Hiệu quả cao (ít khám phá)
   - Thời gian chấp nhận được

2. **BFS đảm bảo tối ưu** nhưng kém hiệu quả về không gian

3. **DFS nhanh nhưng cho kết quả kém** - không nên dùng khi cần đường đi ngắn

4. **GBFS hiệu quả nhưng không đảm bảo** - phù hợp khi cần nhanh và chấp nhận không tối ưu

5. **Heuristic tốt là chìa khóa** - Manhattan distance hoạt động tốt cho mê cung 2D

### 7.2. Khuyến nghị sử dụng

**Khi nào dùng A*:**
- Cần đường đi ngắn nhất (đảm bảo)
- Có heuristic tốt
- Cân bằng giữa hiệu quả và tối ưu
- ⭐ **Khuyến nghị mặc định**

**Khi nào dùng BFS:**
- Không có heuristic
- Đảm bảo tối ưu tuyệt đối
- Không gian tìm kiếm nhỏ

**Khi nào dùng GBFS:**
- Cần nhanh
- Chấp nhận không tối ưu
- Heuristic rất tốt

**Khi nào dùng DFS:**
- Chỉ cần tìm **một** nghiệm bất kỳ (không quan tâm tối ưu)
- Không gian tìm kiếm rất sâu
- Tiết kiệm bộ nhớ quan trọng

**Khi nào dùng IDS:**
- Cần tối ưu như BFS
- Tiết kiệm bộ nhớ như DFS
- Không gian tìm kiếm rất lớn

### 7.3. Bài học rút ra

1. **Không có thuật toán nào là tốt nhất cho mọi trường hợp**
2. **Trade-off giữa thời gian, không gian và tối ưu**
3. **Heuristic tốt làm tăng hiệu quả đáng kể**
4. **Cần hiểu rõ đặc điểm bài toán để chọn thuật toán phù hợp**

---

## 8. TÀI LIỆU THAM KHẢO

1. Russell, S., & Norvig, P. (2020). *Artificial Intelligence: A Modern Approach* (4th ed.)
2. Slides bài giảng môn học
3. Notebook thực hành: `Maze_Example.ipynb`
4. Source code: `tree_search_solution.py`, `maze_helper.py`

---

## PHỤ LỤC

### A. Ký hiệu trong hình ảnh mê cung

- 🟦 **S (màu xanh dương):** Điểm xuất phát (Start)
- 🟩 **G (màu xanh lá):** Điểm đích (Goal)
- 🟥 **Đường màu đỏ:** Đường đi tìm được
- 🟧 **Màu cam:** Các ô trong frontier (đang xem xét)
- ⬜ **Màu xám:** Các ô đã khám phá (reached)
- ⬛ **Màu đen:** Tường (không thể đi qua)
- ⬜ **Màu trắng:** Không gian chưa khám phá

### B. Các tham số thí nghiệm

```python
# Heuristic function
heuristic = manhattan

# Mê cung
maze_file = "L_maze.txt"

# Thứ tự khám phá
direction_order = "NESW"  # North, East, South, West

# Tham số khác
debug = False
vis = False  # No animation
```

### C. Các file mê cung khác trong thư mục

- `small_maze.txt` - Mê cung nhỏ để test nhanh
- `medium_maze.txt` - Mê cung vừa (dùng cho đo timing)
- `large_maze.txt` - Mê cung lớn (chỉ một nghiệm)
- `open_maze.txt` - Mê cung mở
- `empty_maze.txt` - Không gian trống
- `loops_maze.txt` - Mê cung có vòng lặp
- `L_maze.txt` - Mê cung hình chữ L (dùng trong báo cáo này)

---

**Ghi chú:** Báo cáo này được tạo dựa trên kết quả thực nghiệm từ notebook `Maze_Example.ipynb` với cấu hình không có animation (vis=False) theo yêu cầu đề bài.
