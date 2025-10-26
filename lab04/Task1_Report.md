# Báo Cáo Task 1: Định nghĩa Bài toán Tìm kiếm
## Adversarial Search: Playing Dots and Boxes

---

## 📋 Mục tiêu Task 1

Task 1 yêu cầu định nghĩa đầy đủ các thành phần của bài toán tìm kiếm (search problem) cho game Dots and Boxes, bao gồm:

1. Trạng thái ban đầu (Initial State)
2. Các hành động (Actions)
3. Mô hình chuyển trạng thái (Transition Model)
4. Kiểm tra trạng thái kết thúc (Terminal Test)
5. Hàm tiện ích (Utility Function)

Ngoài ra, cần ước tính kích thước không gian trạng thái (state space) và cây trò chơi (game tree) mà thuật toán minimax sẽ phải duyệt qua.

---

## 🎯 1. Định nghĩa Các Thành phần của Bài toán Tìm kiếm

### 1.1. Trạng thái ban đầu (Initial State)

**Định nghĩa:**
- Board trống với kích thước n×m (n hàng và m cột các điểm - dots)
- Không có đường nối nào được vẽ: `lines = {}`
- Không có ô nào được hoàn thành: `boxes = {}`

**Biểu diễn dữ liệu:**
```python
initial_state = {
    'size': (n, m),      # Kích thước board (n hàng, m cột dots)
    'lines': {},         # Dictionary lưu các đường đã vẽ
    'boxes': {}          # Dictionary lưu các ô đã hoàn thành
}
```

**Ví dụ với board 4×4:**
```python
initial_state = {
    'size': (4, 4),
    'lines': {},
    'boxes': {}
}
```

---

### 1.2. Các hành động (Actions)

**Định nghĩa:**
Một hành động là việc vẽ một đường thẳng giữa hai điểm kề nhau trên board.

**Biểu diễn:**
Mỗi hành động được biểu diễn bởi bộ ba `(orientation, row, col)`:
- `orientation`: 
  - `'h'` (horizontal - ngang): đường nối 2 điểm theo chiều ngang
  - `'v'` (vertical - dọc): đường nối 2 điểm theo chiều dọc
- `row`, `col`: Tọa độ điểm bắt đầu của đường (bắt đầu từ 0)

**Ví dụ:**
- `('h', 0, 0)`: Đường ngang từ điểm (0,0) đến điểm (0,1)
- `('v', 0, 0)`: Đường dọc từ điểm (0,0) đến điểm (1,0)
- `('h', 2, 1)`: Đường ngang từ điểm (2,1) đến điểm (2,2)

**Ràng buộc:**
- Mỗi đường chỉ được vẽ một lần
- Đường phải nằm trong giới hạn board
- Đường phải nối 2 điểm kề nhau

---

### 1.3. Mô hình chuyển trạng thái (Transition Model)

**Hàm:** `result(state, action, player)` → trả về trạng thái mới

**Quy trình xử lý:**

1. **Vẽ đường mới:**
   - Thêm action vào `lines` dictionary
   - Đánh dấu đường đã được vẽ

2. **Kiểm tra hoàn thành ô:**
   - Kiểm tra các ô xung quanh đường vừa vẽ
   - Một ô được hoàn thành khi có đủ 4 cạnh:
     - Top: `('h', row, col)`
     - Bottom: `('h', row+1, col)`
     - Left: `('v', row, col)`
     - Right: `('v', row, col+1)`

3. **Xử lý ô hoàn thành:**
   - Nếu có ô được hoàn thành:
     - Thêm ô vào `boxes` dictionary với key là `(row, col)` và value là `player`
     - Player hiện tại được quyền **đi tiếp** (không đổi lượt)
   - Nếu không có ô nào hoàn thành:
     - Chuyển lượt cho đối thủ (player → -player)

**Đặc điểm quan trọng:**
- Rule "đi tiếp khi hoàn thành ô" là **đặc trưng** của Dots and Boxes
- Một player có thể liên tiếp hoàn thành nhiều ô trong cùng lượt đi
- Điều này tạo ra các "chain" hoàn thành ô, ảnh hưởng lớn đến chiến thuật

**Pseudo-code:**
```
function result(state, action, player):
    new_state = copy(state)
    new_state.lines[action] = True
    
    completed_boxes = check_box_completion(new_state, action)
    
    if len(completed_boxes) > 0:
        for box in completed_boxes:
            new_state.boxes[box] = player
        next_player = player  // Đi tiếp
    else:
        next_player = -player  // Đổi lượt
    
    return (new_state, next_player)
```

---

### 1.4. Kiểm tra trạng thái kết thúc (Terminal Test)

**Định nghĩa:**
Trạng thái là terminal khi **không còn hành động hợp lệ nào** (tất cả các đường đã được vẽ).

**Điều kiện:**
Game kết thúc khi: `số đường đã vẽ = tổng số đường có thể vẽ`

**Công thức tính tổng số đường:**
Với board kích thước n×m (n hàng, m cột dots):
- Số đường ngang tối đa: **n × (m-1)**
- Số đường dọc tối đa: **(n-1) × m**
- **Tổng số đường:** `2nm - n - m`

**Ví dụ:**
| Board Size | n | m | Đường ngang | Đường dọc | Tổng đường |
|------------|---|---|-------------|-----------|------------|
| 2×2 | 2 | 2 | 2×1 = 2 | 1×2 = 2 | 4 |
| 3×3 | 3 | 3 | 3×2 = 6 | 2×3 = 6 | 12 |
| 4×4 | 4 | 4 | 4×3 = 12 | 3×4 = 12 | 24 |
| 5×5 | 5 | 5 | 5×4 = 20 | 4×5 = 20 | 40 |

**Implementation:**
```python
def terminal(board):
    n, m = board['size']
    total_possible_lines = 2*n*m - n - m
    return len(board['lines']) == total_possible_lines
```

---

### 1.5. Hàm tiện ích cho trạng thái kết thúc (Utility)

**Định nghĩa:**
`utility(state, player)` trả về giá trị của trạng thái terminal cho player hiện tại.

**Cách tính:**
```
utility = số_ô_của_player - số_ô_của_đối_thủ
```

**Giá trị trả về:**
- **Dương (> 0):** Player thắng
- **0:** Hòa  
- **Âm (< 0):** Player thua

**Ví dụ:**
| Player boxes | Opponent boxes | Utility | Kết quả |
|--------------|----------------|---------|---------|
| 3 | 1 | +2 | Player thắng |
| 2 | 2 | 0 | Hòa |
| 1 | 3 | -2 | Player thua |

**Implementation:**
```python
def utility(board, player):
    player_boxes = sum(1 for p in board['boxes'].values() if p == player)
    opponent_boxes = sum(1 for p in board['boxes'].values() if p == -player)
    return player_boxes - opponent_boxes
```

---

## 📊 2. Đặc điểm của Bài toán

Dots and Boxes là một bài toán tìm kiếm đối kháng (adversarial search) với các đặc điểm:

### 2.1. Zero-Sum Game
- Lợi ích của một player là mất mát của player kia
- Tổng utility của cả 2 players luôn bằng 0
- Không có khả năng "win-win"

### 2.2. Perfect Information
- Cả hai players đều thấy toàn bộ trạng thái board
- Không có thông tin ẩn
- Không có yếu tố bất định

### 2.3. Deterministic
- Không có yếu tố ngẫu nhiên
- Mỗi action dẫn đến một trạng thái xác định duy nhất
- Hoàn toàn dựa vào chiến thuật

### 2.4. Sequential  
- Người chơi đi lần lượt
- **Ngoại lệ:** Player đi tiếp khi hoàn thành ô (special rule)

---

## 📏 3. Ước tính Kích thước Không gian Trạng thái (State Space)

### 3.1. Định nghĩa
**State Space** là tập hợp tất cả các trạng thái có thể đạt được từ trạng thái ban đầu.

### 3.2. Công thức Giới hạn Trên (Upper Bound)

Mỗi đường có 2 trạng thái: **đã vẽ** hoặc **chưa vẽ**

→ Số tổ hợp lý thuyết: **2^(tổng số đường)**

```
Upper Bound = 2^(2nm - n - m)
```

### 3.3. Bảng Ước tính

| Board Size | Tổng đường | Upper Bound | Ký hiệu khoa học |
|------------|------------|-------------|------------------|
| 2×2 | 4 | 2^4 = 16 | 1.6 × 10^1 |
| 3×3 | 12 | 2^12 = 4,096 | 4.1 × 10^3 |
| 4×4 | 24 | 2^24 ≈ 16.7 triệu | 1.7 × 10^7 |
| 5×5 | 40 | 2^40 ≈ 1.1 nghìn tỷ | 1.1 × 10^12 |

### 3.4. Thực tế vs Lý thuyết

**Giới hạn trên chỉ là ước tính lý thuyết!**

Trong thực tế:
- ❌ Không phải tất cả tổ hợp đều hợp lệ
- ❌ Nhiều trạng thái không thể đạt được do luật chơi
- ❌ Rule "đi tiếp khi hoàn thành ô" hạn chế đường đi

**Ước tính thực tế:**
- Board 3×3: ~vài trăm trạng thái thực sự có thể đạt được (so với 4,096 lý thuyết)
- Số trạng thái thực tế ≈ **5-20%** upper bound

### 3.5. Độ phức tạp

State space tăng theo **hàm mũ** với kích thước board:

```
State Space ∝ 2^(board_size)
```

→ Đây là lý do tại sao cần các kỹ thuật tối ưu như:
- Alpha-beta pruning
- Depth-limited search  
- Heuristic evaluation

---

## 🌳 4. Ước tính Kích thước Cây Trò chơi (Game Tree)

### 4.1. Game Tree vs State Space

**Khác biệt quan trọng:**

| | State Space | Game Tree |
|---|---|---|
| **Định nghĩa** | Tập các trạng thái có thể | Cây các chuỗi nước đi |
| **Quan tâm** | Trạng thái (không quan tâm thứ tự) | Chuỗi actions (quan tâm thứ tự) |
| **Kích thước** | Nhỏ hơn | **LỚN HƠN NHIỀU** |
| **Ví dụ** | {A, B, C} | A→B→C, A→C→B, B→A→C,... |

### 4.2. Các Thông số của Game Tree

#### a) Độ sâu (Depth)
- **Độ sâu tối đa** = Tổng số đường cần vẽ
- Do rule "đi tiếp khi hoàn thành ô", depth thực tế có thể nhỏ hơn

**Ví dụ:**
| Board | Tổng đường | Max Depth |
|-------|------------|-----------|
| 2×2 | 4 | 4 |
| 3×3 | 12 | 12 |
| 4×4 | 24 | 24 |
| 5×5 | 40 | 40 |

#### b) Branching Factor
- **Bước đầu:** Nhiều lựa chọn = tổng số đường
- **Bước cuối:** 1 lựa chọn
- **Trung bình:** ≈ (tổng số đường) / 2

**Ví dụ board 3×3:**
- Bước 1: 12 choices
- Bước 6: ~6 choices  
- Bước 12: 1 choice
- **Average:** ~6 choices/bước

#### c) Số Nodes trong Tree

**Công thức xấp xỉ:**
```
Số nodes ≈ b^d
```
Với:
- b = average branching factor
- d = depth

**Ước tính chi tiết hơn:**
```
Số nodes = Σ(i=0 to d) b_i
```
Với b_i giảm dần theo depth

### 4.3. Bảng Ước tính Game Tree Size

| Board | Đường | Boxes | Max Depth | Avg Branch | Ước tính Nodes |
|-------|-------|-------|-----------|------------|----------------|
| 2×2 | 4 | 1 | 4 | 2.0 | ~10^1 |
| 3×3 | 12 | 4 | 12 | 6.0 | ~10^13 |
| 4×4 | 24 | 9 | 24 | 12.0 | ~10^32 |
| 5×5 | 40 | 16 | 40 | 20.0 | ~10^63 |

### 4.4. Tại sao Minimax không khả thi cho Board lớn?

**Ví dụ Board 4×4:**
- Ước tính: ~10^32 nodes
- Giả sử kiểm tra **1 tỷ nodes/giây**
- Thời gian cần: 10^32 / 10^9 = 10^23 giây
- ≈ **10^15 năm!** 🤯

**Kết luận:**
→ Cần các kỹ thuật tối ưu:
1. **Alpha-beta pruning:** Giảm nodes cần duyệt
2. **Depth-limited search:** Chỉ duyệt đến độ sâu nhất định  
3. **Heuristic evaluation:** Đánh giá trạng thái không terminal
4. **Move ordering:** Duyệt nước tốt trước

---

## 📈 5. So sánh với Các Game Khác

| Game | Board Size | Game Tree Size | Độ khó |
|------|------------|----------------|---------|
| **Tic-Tac-Toe** | 3×3 | ~10^5 | Dễ ✅ |
| **Dots & Boxes 3×3** | 3×3 | ~10^13 | Trung bình 🟡 |
| **Connect Four** | 7×6 | ~10^14 | Trung bình 🟡 |
| **Checkers** | 8×8 | ~10^20 | Khó 🔴 |
| **Chess** | 8×8 | ~10^120 | Rất khó 🔴🔴 |
| **Go** | 19×19 | ~10^360 | Cực khó 🔴🔴🔴 |

**Nhận xét:**
- Dots & Boxes 3×3 khó hơn Tic-tac-toe khoảng **10^8 lần**
- Cần thuật toán tối ưu để chơi hiệu quả

---

## 💡 6. Kết luận Task 1

### 6.1. Những gì đã hoàn thành

✅ **Định nghĩa đầy đủ 5 thành phần** của search problem:
1. Initial State
2. Actions  
3. Transition Model (với xử lý đặc biệt rule "đi tiếp")
4. Terminal Test
5. Utility Function

✅ **Phân tích độ phức tạp:**
- State Space: Tăng theo 2^(board_size)
- Game Tree: Tăng theo b^d (cực lớn)

✅ **Hiểu rõ thách thức:**
- Minimax thuần không khả thi cho board > 3×3
- Cần các kỹ thuật tối ưu

### 6.2. Insights quan trọng

1. **Special Rule Impact:**
   - Rule "đi tiếp khi hoàn thành ô" làm phức tạp search
   - Cần xử lý cẩn thận trong minimax

2. **Exponential Complexity:**
   - Game tree tăng cực nhanh
   - Board 4×4 đã không khả thi với minimax thuần

3. **Trade-offs cần thiết:**
   - Depth vs Time
   - Accuracy vs Speed
   - Complete search vs Heuristic

### 6.3. Hướng phát triển

Task tiếp theo sẽ triển khai:
- ✅ Game environment
- ✅ Minimax với alpha-beta pruning
- ✅ Heuristic evaluation
- ✅ Các optimizations

---

## 📚 Tài liệu Tham khảo

1. **Russell & Norvig** - "Artificial Intelligence: A Modern Approach"
   - Chapter 5: Adversarial Search
   - Section 5.2: Optimal Decisions in Games

2. **Wikipedia** - Dots and Boxes
   - https://en.wikipedia.org/wiki/Dots_and_Boxes

3. **Online Demo** - Play Dots and Boxes
   - https://www.math.ucla.edu/~tom/Games/dots&boxes.html

4. **Course Materials** - CS7320-AI
   - Tic-tac-toe examples và implementations
   - Minimax và Alpha-beta pruning

---

**Ngày hoàn thành:** October 25, 2025  
**Sinh viên thực hiện:** [Tên của bạn]  
**Môn học:** Trí Tuệ Nhân Tạo Nâng Cao  
**Giảng viên:** GV Đỗ Như Tài
