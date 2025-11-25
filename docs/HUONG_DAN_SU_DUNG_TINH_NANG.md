# 📖 HƯỚNG DẪN SỬ DỤNG TÍNH NĂNG STRIX

## Tài Liệu Hướng Dẫn Chi Tiết Về Cách Sử Dụng Từng Tính Năng

---

## 📑 MỤC LỤC

1. [Giới Thiệu](#1-giới-thiệu)
2. [Cài Đặt và Cấu Hình](#2-cài-đặt-và-cấu-hình)
3. [Hướng Dẫn Từng Tính Năng](#3-hướng-dẫn-từng-tính-năng)
4. [Quy Trình Làm Việc Thực Tế](#4-quy-trình-làm-việc-thực-tế)
5. [Ví Dụ Thực Tế](#5-ví-dụ-thực-tế)
6. [Câu Hỏi Thường Gặp](#6-câu-hỏi-thường-gặp)

---

## 1. GIỚI THIỆU

### 1.1 Strix Là Gì?

Strix là hệ thống AI agents mã nguồn mở hoạt động như các chuyên gia bảo mật (hackers). Hệ thống này:
- Chạy mã code động để phát hiện lỗ hổng
- Xác nhận lỗ hổng thông qua Proof-of-Concept (PoC)
- Tạo báo cáo chi tiết với hướng dẫn khắc phục

### 1.2 Khi Nào Sử Dụng Strix?

| Tình Huống | Phù Hợp | Ghi Chú |
|------------|---------|---------|
| Kiểm tra bảo mật ứng dụng web | ✅ | Mục đích chính |
| Kiểm tra API REST/GraphQL | ✅ | Hỗ trợ đầy đủ |
| Đánh giá bảo mật trước khi deploy | ✅ | Tích hợp CI/CD |
| Thu thập dữ liệu từ website | ❌ | Không phù hợp |
| Tấn công hệ thống không được phép | ❌ | Vi phạm pháp luật |

---

## 2. CÀI ĐẶT VÀ CẤU HÌNH

### 2.1 Yêu Cầu Hệ Thống

```
- Python 3.12+
- Docker (đang chạy)
- LLM Provider key (OpenAI, Anthropic, v.v.)
- RAM: tối thiểu 8GB
- Disk: tối thiểu 10GB trống
```

### 2.2 Cài Đặt

```bash
# Cài đặt Strix
pipx install strix-agent

# Hoặc sử dụng pip
pip install strix-agent
```

### 2.3 Cấu Hình

```bash
# Cấu hình bắt buộc
export STRIX_LLM="openai/gpt-5"        # Hoặc anthropic/claude-sonnet-4-5
export LLM_API_KEY="your-api-key"

# Cấu hình tùy chọn
export LLM_API_BASE="your-api-base"     # Cho local model
export PERPLEXITY_API_KEY="your-key"    # Cho tính năng tìm kiếm web
```

---

## 3. HƯỚNG DẪN TỪNG TÍNH NĂNG

### 3.1 Browser Automation (Tự Động Hóa Trình Duyệt)

#### Mục Đích
Kiểm tra các lỗ hổng phía client như XSS, CSRF, và vấn đề xác thực.

#### Các Hành Động Có Sẵn

| Hành Động | Mô Tả | Ví Dụ |
|-----------|-------|-------|
| `launch` | Khởi động trình duyệt | `browser_action(action="launch")` |
| `goto` | Điều hướng URL | `browser_action(action="goto", url="https://...")` |
| `click` | Click tọa độ | `browser_action(action="click", coordinate="200,300")` |
| `type` | Nhập văn bản | `browser_action(action="type", text="test")` |
| `scroll_down` | Cuộn xuống | `browser_action(action="scroll_down")` |
| `execute_js` | Chạy JavaScript | `browser_action(action="execute_js", code="...")` |
| `view_source` | Xem mã nguồn | `browser_action(action="view_source")` |
| `get_console_logs` | Lấy console logs | `browser_action(action="get_console_logs")` |
| `save_pdf` | Lưu PDF | `browser_action(action="save_pdf")` |

#### Ví Dụ: Kiểm Tra XSS

```python
# Bước 1: Khởi động trình duyệt
browser_action(action="launch")

# Bước 2: Truy cập trang đăng ký
browser_action(action="goto", url="https://target.com/register")

# Bước 3: Nhập payload XSS
browser_action(action="click", coordinate="300,200")  # Click vào input
browser_action(action="type", text="<script>alert('XSS')</script>")

# Bước 4: Submit form
browser_action(action="click", coordinate="400,400")  # Click submit

# Bước 5: Kiểm tra kết quả
browser_action(action="get_console_logs")
```

#### Kết Quả Mong Đợi
- Nếu XSS tồn tại: Console logs sẽ hiển thị alert hoặc script được thực thi
- Nếu không có XSS: Không có dấu hiệu script chạy

---

### 3.2 HTTP Proxy (Caido Integration)

#### Mục Đích
Chặn, phân tích và sửa đổi traffic HTTP/HTTPS.

#### Các Chức Năng

| Chức Năng | Mô Tả |
|-----------|-------|
| `list_requests()` | Liệt kê requests với bộ lọc |
| `view_request()` | Xem chi tiết request/response |
| `send_request()` | Gửi HTTP request mới |
| `repeat_request()` | Lặp lại với modifications |
| `scope_rules()` | Quản lý scope |
| `list_sitemap()` | Xem sitemap |

#### Ví Dụ: Kiểm Tra IDOR

```python
# Bước 1: Liệt kê requests đến API users
requests = list_requests(
    httpql_filter="path contains '/api/users/'",
    sort_by="timestamp",
    sort_order="desc"
)

# Bước 2: Xem chi tiết một request
response = view_request(
    request_id="req_123",
    part="response"
)

# Bước 3: Thử truy cập user khác (kiểm tra IDOR)
idor_test = repeat_request(
    request_id="req_123",
    modifications={
        "path": "/api/users/999"  # ID của user khác
    }
)

# Bước 4: Phân tích kết quả
# Nếu response 200 với data của user khác → IDOR exists!
```

---

### 3.3 Terminal Execution

#### Mục Đích
Chạy các lệnh shell trong môi trường Kali Linux.

#### Cú Pháp

```python
terminal_execute(
    command: str,           # Lệnh cần chạy
    is_input: bool = False, # Là input cho lệnh đang chạy
    timeout: float = 60,    # Thời gian chờ (giây)
    terminal_id: str = None # ID của session
)
```

#### Ví Dụ: Reconnaissance

```python
# Quét port với nmap
terminal_execute(
    command="nmap -sV -sC 192.168.1.100",
    timeout=300
)

# Tìm subdomain
terminal_execute(
    command="subfinder -d example.com -silent",
    timeout=120
)

# Kiểm tra SSL/TLS
terminal_execute(
    command="testssl.sh https://example.com",
    timeout=180
)

# Enum directories
terminal_execute(
    command="gobuster dir -u https://example.com -w /usr/share/wordlists/dirb/common.txt",
    timeout=600
)
```

---

### 3.4 Python Runtime

#### Mục Đích
Viết và chạy code Python tùy chỉnh.

#### Các Hành Động

| Hành Động | Mô Tả |
|-----------|-------|
| `new_session` | Tạo session mới |
| `execute` | Chạy code |
| `close` | Đóng session |
| `list_sessions` | Liệt kê sessions |

#### Ví Dụ: Kiểm Tra SQL Injection

```python
# Bước 1: Tạo session
python_action(action="new_session", session_id="sqli_test")

# Bước 2: Viết exploit
python_action(
    action="execute",
    session_id="sqli_test",
    code="""
import requests

url = "https://target.com/api/search"
payloads = [
    "' OR '1'='1",
    "1' ORDER BY 1--",
    "1' UNION SELECT NULL--",
    "1; WAITFOR DELAY '0:0:5'--"
]

for payload in payloads:
    response = requests.get(url, params={"q": payload})
    print(f"Payload: {payload}")
    print(f"Status: {response.status_code}")
    print(f"Length: {len(response.text)}")
    print("---")
"""
)
```

---

### 3.5 Web Search

#### Mục Đích
Tìm kiếm thông tin bảo mật, CVE, và kỹ thuật mới.

#### Ví Dụ

```python
# Tìm CVE cho phần mềm cụ thể
web_search(query="CVE Apache 2.4.49 path traversal")

# Tìm kỹ thuật bypass
web_search(query="WAF bypass SQL injection 2024")

# Tìm exploit đã biết
web_search(query="Log4j RCE exploit poc")
```

---

### 3.6 Notes Management

#### Mục Đích
Theo dõi phát hiện trong quá trình kiểm tra.

#### Các Categories

- `general` - Ghi chú chung
- `findings` - Phát hiện lỗ hổng
- `methodology` - Phương pháp
- `todo` - Việc cần làm
- `questions` - Câu hỏi
- `plan` - Kế hoạch

#### Ví Dụ

```python
# Ghi chú phát hiện mới
create_note(
    title="SQL Injection trong /api/search",
    content="Phát hiện time-based SQLi trong parameter 'q'",
    category="findings",
    tags=["sqli", "critical"],
    priority="urgent"
)

# Liệt kê các phát hiện
list_notes(category="findings")
```

---

### 3.7 Vulnerability Reporting

#### Mục Đích
Tạo báo cáo lỗ hổng chính thức.

#### Mức Độ Nghiêm Trọng

| Level | CVSS | Ý Nghĩa |
|-------|------|---------|
| `critical` | 9.0-10.0 | RCE, Auth bypass |
| `high` | 7.0-8.9 | SQLi, XSS stored |
| `medium` | 4.0-6.9 | Info disclosure |
| `low` | 0.1-3.9 | Minor issues |
| `info` | N/A | Informational |

#### Ví Dụ

```python
create_vulnerability_report(
    title="Stored XSS trong Comment System",
    content="""
## Mô Tả
Stored XSS cho phép inject script vào comments.

## Bước Tái Hiện
1. Đăng nhập
2. Truy cập /blog/post/1
3. Nhập: <script>alert(document.cookie)</script>
4. Submit
5. Reload → XSS triggered

## Impact
- Session hijacking
- Account takeover

## Khắc Phục
- Sanitize input
- Implement CSP
    """,
    severity="high"
)
```

---

### 3.8 Multi-Agent System

#### Mục Đích
Tạo nhiều agents chuyên biệt để kiểm tra song song.

#### Các Chức Năng

| Chức Năng | Mô Tả |
|-----------|-------|
| `create_agent()` | Tạo sub-agent |
| `view_agent_graph()` | Xem cấu trúc |
| `send_message_to_agent()` | Gửi tin nhắn |
| `agent_finish()` | Hoàn thành |

#### Ví Dụ: Kiểm Tra Song Song

```python
# Agent 1: Kiểm tra SQLi
create_agent(
    task="Kiểm tra SQL Injection trên tất cả API endpoints",
    name="SQLi_Agent",
    prompt_modules="sql_injection"
)

# Agent 2: Kiểm tra XSS
create_agent(
    task="Kiểm tra XSS trên tất cả forms",
    name="XSS_Agent",
    prompt_modules="xss"
)

# Agent 3: Kiểm tra Authentication
create_agent(
    task="Kiểm tra JWT vulnerabilities",
    name="Auth_Agent",
    prompt_modules="authentication_jwt"
)
```

---

## 4. QUY TRÌNH LÀM VIỆC THỰC TẾ

### 4.1 Quy Trình Chuẩn

```
┌──────────────────────────────────────────────────────────┐
│ BƯỚC 1: RECONNAISSANCE (Thu thập thông tin)              │
│ ──────────────────────────────────────────────────────── │
│ • Sử dụng Terminal để quét port, tìm subdomain           │
│ • Sử dụng Web Search để tìm CVE liên quan                │
│ • Sử dụng Browser để khám phá UI                         │
└──────────────────────────────────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────┐
│ BƯỚC 2: MAPPING (Lập bản đồ ứng dụng)                    │
│ ──────────────────────────────────────────────────────── │
│ • Sử dụng HTTP Proxy để capture tất cả endpoints         │
│ • Sử dụng Browser để duyệt toàn bộ chức năng             │
│ • Ghi chú vào Notes                                      │
└──────────────────────────────────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────┐
│ BƯỚC 3: VULNERABILITY SCANNING (Quét lỗ hổng)            │
│ ──────────────────────────────────────────────────────── │
│ • Tạo Multi-Agent cho từng loại lỗ hổng                  │
│ • Agents chạy song song                                  │
│ • Sử dụng Python Runtime để viết exploit                 │
└──────────────────────────────────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────┐
│ BƯỚC 4: EXPLOITATION (Khai thác)                         │
│ ──────────────────────────────────────────────────────── │
│ • Xác nhận lỗ hổng bằng PoC                              │
│ • Chụp screenshot làm bằng chứng                         │
│ • Ghi lại các bước tái hiện                              │
└──────────────────────────────────────────────────────────┘
                            │
                            ▼
┌──────────────────────────────────────────────────────────┐
│ BƯỚC 5: REPORTING (Báo cáo)                              │
│ ──────────────────────────────────────────────────────── │
│ • Tạo Vulnerability Report cho từng lỗ hổng              │
│ • Tổng hợp tất cả findings                               │
│ • Đề xuất khắc phục                                      │
└──────────────────────────────────────────────────────────┘
```

### 4.2 Thời Gian Ước Tính

| Giai Đoạn | Thời Gian | Ghi Chú |
|-----------|-----------|---------|
| Reconnaissance | 1-2 giờ | Tự động hóa cao |
| Mapping | 2-3 giờ | Cần review thủ công |
| Scanning | 4-8 giờ | Multi-agent song song |
| Exploitation | 2-4 giờ | Phụ thuộc số lỗ hổng |
| Reporting | 1-2 giờ | Tự động tạo báo cáo |
| **Tổng** | **10-19 giờ** | So với 2-4 tuần thủ công |

---

## 5. VÍ DỤ THỰC TẾ

### 5.1 Kiểm Tra E-Commerce

```bash
# Lệnh khởi chạy
strix --target https://shop.example.com \
      --instruction "Focus on payment processing, cart, and user authentication"
```

### 5.2 Kiểm Tra Banking App

```bash
strix --target https://banking.example.com \
      --instruction "Test fund transfer, session management, and JWT tokens. \
                     Use credentials: testuser:TestPass123"
```

### 5.3 Tích Hợp CI/CD

```yaml
# .github/workflows/security.yml
name: Security Scan

on:
  pull_request:

jobs:
  strix-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install Strix
        run: pipx install strix-agent
        
      - name: Run Security Scan
        env:
          STRIX_LLM: ${{ secrets.STRIX_LLM }}
          LLM_API_KEY: ${{ secrets.LLM_API_KEY }}
        run: strix -n -t ./
        
      - name: Upload Report
        uses: actions/upload-artifact@v4
        with:
          name: security-report
          path: strix_runs/
```

---

## 6. CÂU HỎI THƯỜNG GẶP

### Q1: Strix có thể thu thập dữ liệu từ website không?

**A:** Không. Strix được thiết kế cho kiểm thử bảo mật, không phải web scraping. Sử dụng sai mục đích có thể vi phạm pháp luật.

### Q2: Cần API key của LLM nào?

**A:** Khuyến nghị sử dụng:
- OpenAI GPT-5: `openai/gpt-5`
- Anthropic Claude Sonnet 4.5: `anthropic/claude-sonnet-4-5`

### Q3: Strix có thể bypass CAPTCHA không?

**A:** Không. Strix không được thiết kế để vượt qua các biện pháp bảo mật như CAPTCHA.

### Q4: Chi phí sử dụng Strix?

**A:** 
- Strix bản thân miễn phí (open-source)
- Chi phí LLM API: ~$100-500/assessment (tùy độ phức tạp)
- Hoặc sử dụng cloud version tại app.usestrix.com

### Q5: Có thể chạy Strix trên Windows không?

**A:** Có, nhưng cần Docker. Khuyến nghị sử dụng Linux/macOS để có trải nghiệm tốt nhất.

---

> ⚠️ **LƯU Ý QUAN TRỌNG**
> 
> Chỉ sử dụng Strix để kiểm tra các ứng dụng mà bạn:
> - Là chủ sở hữu
> - Được cấp phép bằng văn bản
> - Đang trong chương trình Bug Bounty
> 
> Vi phạm có thể dẫn đến trách nhiệm pháp lý.

---

*Tài liệu được cập nhật: Tháng 11/2025*
*Phiên bản Strix: 0.3.5*
