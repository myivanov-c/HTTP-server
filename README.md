# Webserv — HTTP/1.1 Web Server in C++98

A lightweight, high-performance, non-blocking **HTTP/1.1 web server** built from scratch in **C++98**, inspired by **NGINX**. This project implements low-level socket programming, I/O multiplexing, custom configuration parsing, CGI execution, and session management without external networking or parsing libraries.

## Key Features

- **Non-Blocking Event-Driven Architecture:** Handles multiple concurrent client connections within a single process using I/O multiplexing (`poll` / `epoll`), preventing blocking reads or writes.
- **Custom NGINX-Style Configuration Parser:** Built from scratch with full lexical, syntactic, and semantic validation (rejecting malformed directives or conflicting server blocks).
    - Multiple `server` blocks (virtual hosts by `listen` port/host and `server_name`).
    - Prefix and exact `location` routing with per-route allowed HTTP methods, root overrides, default index files, directory listing (`autoindex`), HTTP redirections (`301`/`302`), custom error pages, and `client_max_body_size` limits.
- **Full HTTP/1.1 Protocol Support:**
    - Methods: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`, `HEAD`, `OPTIONS`.
    - Chunked transfer encoding, persistent connections (`Keep-Alive`), multipart file uploads, and static asset serving with accurate MIME type resolution.
- **Multi-Interpreter CGI Execution (Bonus):**
    - Dynamic content generation via **Python** and **PHP** CGI scripts, passing compliant `CGI/1.1` environment variables (`REQUEST_METHOD`, `QUERY_STRING`, `CONTENT_LENGTH`, `PATH_INFO`, etc.) over pipes with non-blocking I/O and timeout protection.
- **Cookies & Session Management (Bonus):**
    - Server-side session tracking and HTTP `Set-Cookie` / `Cookie` header handling.

## Performance & Stress Testing

Validated under sustained concurrent load using **Siege** (`20` concurrent clients, zero delay):

| Metric | Result |
| --- | --- |
| **Test Duration** | 30 minutes |
| **Total Requests Served** | 27,800,000+ |
| **Throughput** | ~15,000 req/s |
| **Availability** | 100.00% |
| **Failed Transactions** | 0 |

## Getting Started

### Prerequisites

- `c++` compiler supporting `std=c++98` (`g++` or `clang++`)
- `make`
- Optional (for CGI): `python3`, `php-cgi`

### Build & Run

```bash
# Clone the repository
git clone https://github.com/myivanov-c/HTTP-server.git
cd HTTP-server

# Compile the server
make

# Run the server
./webserver site.conf
```

### Makefile Targets

- `make` / `make all` — Compiles the `webserver` binary.
- `make clean` — Removes object files.
- `make fclean` — Removes object files and the compiled binary.
- `make re` — Rebuilds the project from scratch.

## Testing & Verification

- **Memory Safety:** Verified with `valgrind --leak-check=full --show-leak-kinds=all` across normal traffic, malformed HTTP requests, and abrupt client disconnects (zero leaks, zero invalid reads/writes).
- **HTTP Compliance & Edge Cases:** Tested with `curl`, browser clients, custom Python test suites (chunked bodies, oversized payloads, invalid methods, timeout handling), and `siege`.