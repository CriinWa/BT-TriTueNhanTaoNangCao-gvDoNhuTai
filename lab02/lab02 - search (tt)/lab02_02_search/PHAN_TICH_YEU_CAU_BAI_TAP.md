# PHÂN TÍCH YÊU CẦU BÀI TẬP: MAZE.IPYNB

**Notebook:** Maze.ipynb  
**Chủ đề:** Solving a Maze Using a Goal-based Agent  
**Môn học:** CS7320 - Artificial Intelligence

---

## 📋 TỔNG QUAN BÀI TẬP

### Điểm số
- **Sinh viên đại học (Undergraduate):** 100 điểm + 5 điểm bonus
- **Sinh viên cao học (Graduate):** 110 điểm

### Mục tiêu học tập
1. Xây dựng bài toán tìm kiếm với các thành phần: initial state, actions, goal state
2. Cài đặt và so sánh các thuật toán: BFS, DFS, GBFS, A*, IDS
3. Phân tích hiệu suất: path cost, node expansions, depth, memory usage
4. Sử dụng công cụ visualization để debug và phân tích

### Nộp bài
- File notebook (.ipynb)
- File HTML (rendered với tất cả outputs)

---

## 🎯 BỐI CẢNH BÀI TOÁN

### Loại Agent
**Goal-based Agent** - Agent hoạch định đường đi trong mê cung

### Đặc điểm môi trường
1. **Fully Observable** - Agent biết vị trí hiện tại và các hành động có thể
2. **Discrete** - Số trạng thái hữu hạn (các ô trong mê cung)
3. **Deterministic** - Hành động luôn cho kết quả giống nhau
4. **Known** - Agent có bản đồ đầy đủ (biết transition function)

### Open-loop System
- Agent lập kế hoạch trước
- Không cần quan tâm percepts khi thực thi
- Không cần implement environment

### Dữ liệu
- **Mê cung:** Lưu trong file .txt
- **Format:** Ký tự S (Start), G (Goal), X (Wall), khoảng trắng (Path)
- **Vị trí:** Tuple (row, col)

---

## 📚 CÁC TASK CHI TIẾT

## TASK 0: General Requirements [10 điểm]

### Yêu cầu chung:
1. ✅ Sử dụng phiên bản notebook mới nhất
2. ✅ Được dùng: math, numpy, scipy
3. ❌ KHÔNG dùng: thư viện implement sẵn search algorithms
4. ✅ Code đơn giản, không cần OOP phức tạp

### Định dạng notebook:
- ✅ Thêm markdown blocks giải thích
- ✅ Comment trong code
- ✅ Tạo bảng và biểu đồ (matplotlib)
- ❌ KHÔNG hiển thị debug output
- ❌ KHÔNG output quá nhiều
- ✅ File nộp phải readable với đầy đủ figures

### Documentation:
- Comment trong code
- Giải thích cách hoạt động
- Nêu rõ design choices

---

## TASK 1: Defining the Search Problem [10 điểm]

### 1.1. Định nghĩa các thành phần

Cần định nghĩa rõ ràng:

#### **Initial State** (Trạng thái ban đầu)
- Vị trí xuất phát S
- Dạng: (row, col)

#### **Actions** (Hành động)
- Các hướng di chuyển có thể
- Thường là: North, South, East, West
- Phụ thuộc vào vị trí và tường

#### **Transition Model** (Mô hình chuyển đổi)
- Hàm: RESULT(state, action) → new_state
- Quy tắc di chuyển
- Kiểm tra tường

#### **Goal State** (Trạng thái đích)
- Vị trí G
- Điều kiện kết thúc

#### **Path Cost** (Chi phí đường đi)
- Cách tính chi phí
- Thường: số bước di chuyển

### 1.2. Ước lượng kích thước bài toán

Cần ước tính và giải thích cách tính:

#### **n: State Space Size** (Kích thước không gian trạng thái)
- Số ô trống trong mê cung
- Cách tính: Đếm ô không phải tường

#### **d: Depth of Optimal Solution** (Độ sâu nghiệm tối ưu)
- Độ dài đường đi ngắn nhất
- Cách tính: Chạy BFS

#### **m: Maximum Depth of Tree** (Độ sâu tối đa của cây)
- Độ dài đường dài nhất có thể
- Cách tính: Số ô trống - 1 (hoặc chạy thử)

#### **b: Maximum Branching Factor** (Hệ số phân nhánh tối đa)
- Số hành động tối đa tại một trạng thái
- Maze: tối đa 4 (N, S, E, W)
- Thực tế: thường < 4 do có tường

---

## TASK 2: Uninformed Search - BFS & DFS [40 điểm]

### 2.1. Implementation Requirements

#### **BFS (Breadth-First Search)**

**Yêu cầu:**
- ✅ Theo pseudocode trong textbook/slides
- ✅ Sử dụng `frontier` (FIFO Queue)
- ✅ Sử dụng `reached` data structure
- ✅ Lưu thông tin trong tree, KHÔNG trong map
- ✅ Trả về path từ root đến goal

**Cấu trúc dữ liệu:**
```python
frontier = Queue()  # FIFO
reached = set()     # Hoặc dict để lưu node
```

**Metrics cần đo:**
- Path cost (số bước)
- Number of nodes expanded
- Maximum tree depth
- Maximum frontier size
- Maximum nodes in memory

#### **DFS (Depth-First Search)**

**Yêu cầu đặc biệt:**
- ⚠️ **KHÔNG dùng `reached` structure** (để tiết kiệm bộ nhớ)
- ✅ Dùng Stack (LIFO)
- ✅ Implement **cycle checking** (tránh infinite loop)
- ✅ Release memory cho nodes không cần nữa
- ⚠️ Cycle checking chỉ trong path hiện tại (không toàn bộ tree)

**Tại sao không dùng `reached`?**
```
BFS với reached: O(b^d) space
DFS không reached: O(bm) space  ← Tiết kiệm nhiều!
DFS với reached: O(b^m) space   ← Tệ nhất!
```

**Cycle checking:**
- Chỉ kiểm tra trong path từ root đến node hiện tại
- Không kiểm tra toàn bộ explored nodes
- Thách thức: Open spaces khó kiểm tra

**Cấu trúc dữ liệu:**
```python
frontier = Stack()  # LIFO
# KHÔNG có reached structure
# Cycle checking: kiểm tra parent chain
```

### 2.2. Discussion Questions

#### Câu hỏi 1: Loop/Cycle Handling
**"How does BFS and DFS (without a reached data structure) deal with loops (cycles)?"**

Cần trả lời:
- BFS: Dùng `reached` → không bao giờ visit lại node
- DFS: Chỉ cycle checking trong path → vẫn có thể visit lại node (qua path khác)
- So sánh ưu/nhược điểm

#### Câu hỏi 2: Completeness & Optimality
**"Are your implementations complete and optimal? Explain why."**

Cần phân tích:

**BFS:**
- Complete: ✅ Có (tìm được nghiệm nếu tồn tại)
- Optimal: ✅ Có (tìm đường ngắn nhất)

**DFS:**
- Complete: ⚠️ Có với cycle checking, Không nếu có cycle
- Optimal: ❌ Không (tìm nghiệm đầu tiên, không chắc ngắn nhất)

#### Câu hỏi 3: Time & Space Complexity
**"What is the time and space complexity of each of your implementations?"**

Cần phân tích:

**BFS:**
- Time: O(b^d)
- Space: O(b^d)  ← Tốn bộ nhớ!

**DFS (implementation của bạn):**
- Time: O(b^m)  ← Có thể tệ hơn BFS!
- Space: O(bm)  ← Tiết kiệm bộ nhớ!

**So sánh space complexity:**
- BFS lưu tất cả nodes ở mỗi level
- DFS chỉ lưu path hiện tại
- Ví dụ: b=3, d=10 → BFS: ~59,000 nodes, DFS: ~30 nodes

---

## TASK 3: Informed Search - GBFS & A* [20 điểm]

### 3.1. Heuristic Function

**Manhattan Distance:**
```python
h(n) = |x_current - x_goal| + |y_current - y_goal|
```

**Tính chất:**
- Admissible: ✅ (không bao giờ overestimate)
- Consistent: ✅ (thỏa mãn triangle inequality)

### 3.2. Implementation Requirements

#### **GBFS (Greedy Best-First Search)**

**Công thức:**
```
f(n) = h(n)  # Chỉ dùng heuristic
```

**Yêu cầu:**
- ✅ Mở rộng node có h(n) nhỏ nhất
- ✅ Dùng priority queue (min-heap)
- ✅ Dùng `reached` structure
- ✅ Chỉ chú trọng khoảng cách đến goal

**Cấu trúc:**
```python
frontier = PriorityQueue()  # Sort by h(n)
reached = dict()
```

#### **A* Search**

**Công thức:**
```
f(n) = g(n) + h(n)
# g(n): chi phí từ start đến n
# h(n): ước lượng từ n đến goal
```

**Yêu cầu:**
- ✅ Mở rộng node có f(n) nhỏ nhất
- ✅ Dùng priority queue
- ✅ Dùng `reached` structure
- ✅ Cân bằng chi phí thực + ước lượng
- ✅ Cập nhật node nếu tìm được đường ngắn hơn

**Cấu trúc:**
```python
frontier = PriorityQueue()  # Sort by f(n) = g(n) + h(n)
reached = dict()  # Lưu g(n) để so sánh
```

**Node structure cần mở rộng:**
```python
class Node:
    def __init__(self, pos, parent, action, cost):
        self.pos = pos
        self.parent = parent
        self.action = action
        self.cost = cost  # g(n)
        # Thêm:
        self.h = 0        # h(n) - heuristic
        self.f = 0        # f(n) = g(n) + h(n)
```

### 3.3. Discussion Questions

#### Câu hỏi 1: Completeness & Optimality
**"Are your implementations complete and optimal?"**

**GBFS:**
- Complete: ✅ Có (với finite state space)
- Optimal: ❌ Không (tham lam, bỏ sót đường tốt)

**A*:**
- Complete: ✅ Có
- Optimal: ✅ Có (với heuristic admissible)

#### Câu hỏi 2: Time & Space Complexity
**"What is the time and space complexity?"**

**GBFS:**
- Time: O(b^m) - worst case
- Space: O(b^m)
- Thực tế: phụ thuộc heuristic quality

**A*:**
- Time: O(b^d) - với heuristic tốt
- Space: O(b^d)
- Optimal với heuristic admissible

---

## TASK 4: Comparison and Discussion [20 điểm]

### 4.1. Experiments

#### Chạy trên 8 mê cung:
1. **small_maze.txt** - Mê cung nhỏ cơ bản
2. **medium_maze.txt** - Mê cung trung bình
3. **large_maze.txt** - Mê cung lớn (1 solution)
4. **open_maze.txt** - Không gian mở
5. **wall_maze.txt** - Nhiều tường
6. **loops_maze.txt** - Có vòng lặp
7. **empty_maze.txt** - Không gian trống
8. **empty_2_maze.txt** - Không gian trống khác

#### Mỗi combination (thuật toán × mê cung):
Đo và báo cáo:
1. **Path cost** - Chi phí đường đi (số bước)
2. **# of nodes expanded** - Số nodes đã mở rộng
3. **Max tree depth** - Độ sâu tối đa
4. **Max # of nodes in memory** - Số nodes tối đa trong bộ nhớ
5. **Max frontier size** - Kích thước frontier tối đa

### 4.2. Results Table

**Format cho mỗi mê cung:**

```markdown
| algorithm | path cost | # nodes expanded | max depth | max nodes in memory | max frontier |
|-----------|-----------|------------------|-----------|---------------------|--------------|
| BFS       |           |                  |           |                     |              |
| DFS       |           |                  |           |                     |              |
| GBFS      |           |                  |           |                     |              |
| A*        |           |                  |           |                     |              |
```

**Lưu ý:**
- Nếu có lỗi: đánh dấu * và giải thích
- Nếu infinite loop: ghi "N/A*" và giải thích

### 4.3. Visualization

**Yêu cầu:**
- ✅ Hiển thị mê cung với path tìm được
- ✅ Đánh dấu các ô đã visit
- ✅ Đánh dấu path cuối cùng
- ✅ Dùng màu sắc phân biệt

**Có thể dùng:**
```python
mh.show_maze(maze)  # Helper function
mh.show_path(maze, path)  # Custom function
```

### 4.4. Charts

**Tạo biểu đồ so sánh** (dùng matplotlib):

1. **Bar chart:** Path cost theo thuật toán
2. **Bar chart:** Nodes expanded theo thuật toán
3. **Bar chart:** Memory usage theo thuật toán
4. **Line chart:** Performance trên các mê cung khác nhau
5. **Heatmap:** Kết quả tổng hợp

**Ví dụ:**
```python
import matplotlib.pyplot as plt

algorithms = ['BFS', 'DFS', 'GBFS', 'A*']
path_costs = [16, 80, 16, 16]

plt.bar(algorithms, path_costs)
plt.ylabel('Path Cost')
plt.title('Path Cost Comparison - Small Maze')
plt.show()
```

### 4.5. Discussion

**Câu hỏi:** "Discuss the most important lessons you have learned from implementing the different search strategies."

**Cần thảo luận:**

1. **Performance:**
   - Thuật toán nào nhanh/chậm nhất?
   - Thuật toán nào tốn/tiết kiệm bộ nhớ?
   - Trade-off giữa time và space

2. **Optimality:**
   - Thuật toán nào cho kết quả tối ưu?
   - Khi nào cần tối ưu, khi nào không?

3. **Suitability:**
   - Thuật toán nào phù hợp với loại mê cung nào?
   - Open spaces vs walls vs loops

4. **Heuristic Impact:**
   - Manhattan distance có hiệu quả không?
   - GBFS vs A* khác nhau như thế nào?

5. **Challenges:**
   - Vấn đề gì gặp phải khi implement?
   - Cycle checking trong DFS
   - Open spaces

6. **Real-world Applications:**
   - Áp dụng vào bài toán thực tế nào?
   - Lựa chọn thuật toán dựa vào gì?

---

## TASK 5: Advanced - IDS & Multiple Goals

### 5.1. Điểm số
- **Graduate students:** BẮT BUỘC [10 điểm]
- **Undergraduate students:** TÙY CHỌN [+5 điểm bonus]

### 5.2. IDS (Iterative Deepening Search)

#### Yêu cầu:
- ✅ Implement IDS dựa trên DFS
- ✅ Test trên tất cả các mê cung
- ✅ Báo cáo vấn đề nếu có (đặc biệt với open spaces)
- ✅ Giải thích nguyên nhân

#### Cách hoạt động:
```python
def IDS(maze, max_depth):
    for depth in range(max_depth):
        result = DFS(maze, limit=depth)
        if result is not None:
            return result
    return None
```

#### Đặc điểm:
- Complete: ✅ Có (như BFS)
- Optimal: ✅ Có (như BFS)
- Space: O(bd) (như DFS)
- Time: O(b^d) (chấp nhận được)

#### Vấn đề với open spaces:
- Cycle checking khó
- Có thể không tìm được nghiệm
- Cần giải thích và đề xuất giải pháp

### 5.3. Multiple Goals

#### Yêu cầu:
1. ✅ Tạo mê cung mới từ medium maze
2. ✅ Thêm 1-2 goal (G1, G2)
3. ✅ Agent dừng khi tìm được BẤT KỲ goal nào
4. ✅ Test với DFS, BFS, IDS
5. ✅ So sánh kết quả

#### Thí nghiệm:
```python
# Tạo maze với multiple goals
maze_multi = modify_maze(medium_maze)
maze_multi[10, 15] = 'G'  # Goal 1
maze_multi[20, 5] = 'G'   # Goal 2

# Test algorithms
results = {
    'BFS': test_BFS(maze_multi),
    'DFS': test_DFS(maze_multi),
    'IDS': test_IDS(maze_multi)
}
```

#### Phân tích:
**Cần thảo luận:**
- Thuật toán nào tìm được optimal goal?
- Thuật toán nào không?
- Tại sao?

**Kỳ vọng:**
- BFS: Tìm goal gần nhất ✅
- DFS: Tìm goal đầu tiên gặp (không chắc gần) ❌
- IDS: Tìm goal gần nhất ✅

---

## 🎯 CHECKLIST HOÀN THÀNH

### General
- [ ] Notebook có tên sinh viên
- [ ] Khai báo AI tools đã dùng
- [ ] Khẳng định là công việc của mình
- [ ] Code có comments đầy đủ
- [ ] Markdown giải thích rõ ràng
- [ ] Không có debug output
- [ ] File HTML render đúng

### Task 1 [10 điểm]
- [ ] Định nghĩa Initial State
- [ ] Định nghĩa Actions
- [ ] Định nghĩa Transition Model
- [ ] Định nghĩa Goal State
- [ ] Định nghĩa Path Cost
- [ ] Ước lượng n (state space size)
- [ ] Ước lượng d (optimal depth)
- [ ] Ước lượng m (max depth)
- [ ] Ước lượng b (branching factor)

### Task 2 [40 điểm]
- [ ] Implement BFS với `reached`
- [ ] Implement DFS không `reached`
- [ ] DFS có cycle checking
- [ ] Test trên 8 mazes
- [ ] Thảo luận loop handling
- [ ] Thảo luận completeness & optimality
- [ ] Phân tích time & space complexity

### Task 3 [20 điểm]
- [ ] Implement Manhattan distance heuristic
- [ ] Implement GBFS
- [ ] Implement A*
- [ ] Test trên 8 mazes
- [ ] Thảo luận completeness & optimality
- [ ] Phân tích complexity

### Task 4 [20 điểm]
- [ ] Tạo bảng kết quả cho 8 mazes
- [ ] Có đủ 5 metrics mỗi test
- [ ] Visualization các solutions
- [ ] Tạo charts so sánh
- [ ] Discussion chi tiết
- [ ] So sánh algorithms
- [ ] Rút ra bài học

### Task 5 (Advanced)
#### Graduate Students [10 điểm] / Undergrad Bonus [+5]
- [ ] Implement IDS
- [ ] Test IDS trên 8 mazes
- [ ] Báo cáo vấn đề với open spaces
- [ ] Tạo mazes với multiple goals
- [ ] Test DFS, BFS, IDS
- [ ] So sánh optimality
- [ ] Giải thích kết quả

---

## 💡 TIPS & BEST PRACTICES

### Coding Tips

1. **Start Simple:**
   - Implement BFS trước (đơn giản nhất)
   - Test kỹ trên small maze
   - Sau đó modify cho DFS, GBFS, A*

2. **Debugging:**
   - Print frontier size mỗi bước
   - Visualize maze sau mỗi expansion
   - Kiểm tra cycle detection
   - Đếm nodes carefully

3. **Data Structures:**
   ```python
   # BFS
   from collections import deque
   frontier = deque()
   
   # DFS
   frontier = []  # Use as stack
   
   # GBFS/A*
   import heapq
   frontier = []  # Use as heap
   ```

4. **Memory Management:**
   - DFS: del nodes không cần
   - BFS/A*: Cần lưu reached
   - Track memory usage

### Testing Tips

1. **Start Small:**
   - Test trên small_maze trước
   - Verify path đúng
   - Kiểm tra metrics

2. **Edge Cases:**
   - Empty maze (no walls)
   - Loops maze (cycles)
   - Large maze (performance)

3. **Comparison:**
   - Chạy tất cả algorithms trên cùng maze
   - So sánh results
   - Tìm patterns

### Documentation Tips

1. **Comments:**
   ```python
   # Good
   # Check if neighbor is valid and not a wall
   if is_valid(neighbor) and maze[neighbor] != 'X':
   
   # Bad
   # Check neighbor
   ```

2. **Markdown:**
   - Giải thích logic trước code
   - Kết luận sau results
   - Dùng bullet points
   - Dùng tables cho so sánh

3. **Charts:**
   - Clear titles
   - Labeled axes
   - Legends
   - Consistent colors

---

## 🚨 NHỮNG LỖI THƯỜNG GẶP

### Implementation Errors

1. **Storing info in map:**
   ❌ `maze[pos] = 'visited'`
   ✅ `reached.add(pos)`

2. **DFS with reached:**
   ❌ Mất lợi thế memory của DFS
   ✅ Chỉ cycle checking trong path

3. **Heuristic không admissible:**
   ❌ A* không optimal
   ✅ Manhattan distance luôn admissible

4. **Không release memory:**
   ❌ DFS tốn bộ nhớ như BFS
   ✅ Del nodes không cần

### Conceptual Errors

1. **Confusing expanded vs generated:**
   - Expanded: nodes đã xử lý
   - Generated: nodes đã tạo (có thể chưa xử lý)

2. **Tree depth vs path length:**
   - Tree depth: số level trong tree
   - Path length: số edges trong path

3. **Frontier vs reached:**
   - Frontier: nodes đang chờ xử lý
   - Reached: nodes đã được generate

### Reporting Errors

1. **Incomplete tables:**
   - Thiếu metrics
   - Không có explanation cho *

2. **Poor visualization:**
   - Không phân biệt visited vs path
   - Không có legend

3. **Shallow discussion:**
   - Chỉ list numbers
   - Không giải thích patterns
   - Không rút ra insights

---

## 📚 TÀI LIỆU THAM KHẢO

### Textbook
- Russell & Norvig - Artificial Intelligence: A Modern Approach
  - Chapter 3: Solving Problems by Searching
  - Section 3.3: Search Algorithms
  - Section 3.4: Uninformed Search
  - Section 3.5: Informed Search

### Code Examples
- `maze_helper.py` - Helper functions
- `HOWTOs/trees.ipynb` - Tree structure examples
- `HOWTOs/charts_and_tables.ipynb` - Visualization examples

### Related Notebooks
- `Maze_Example.ipynb` - Comparison without animation
- `Maze_Example-Animation_full.ipynb` - With animation
- `Show_all_mazes.ipynb` - Display all mazes

---

## 🎓 KẾT LUẬN

Bài tập này là một **assignment toàn diện** về search algorithms trong AI, yêu cầu:

1. **Hiểu lý thuyết:** Search problem formulation, algorithms
2. **Implement code:** 4-5 thuật toán với yêu cầu cụ thể
3. **Thí nghiệm:** Test trên 8 mazes khác nhau
4. **Phân tích:** So sánh performance, metrics
5. **Visualization:** Charts, tables, maze displays
6. **Discussion:** Rút ra insights, bài học

**Thời gian dự kiến:** 10-15 giờ (tùy skill level)

**Độ khó:**
- Task 1-3: Trung bình
- Task 4: Khá (nhiều work)
- Task 5: Advanced

**Điểm quan trọng:**
- DFS KHÔNG dùng reached structure
- Cycle checking là bắt buộc
- Documentation và visualization quan trọng
- Discussion phải sâu sắc, có insights

Chúc bạn làm bài tốt! 🚀
