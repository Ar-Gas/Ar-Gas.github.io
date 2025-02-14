---
title: CS144-计算机网络笔记（lab 0 - 4 )
date: 2024-03-30T20:20:00+08:00
lastmod: 2024-03-30T20:20:00+08:00
math: true
image: cover.jpg
categories:
  - 计算机实验
tags:
  - 计算机实验
draft: false
---

最近闲来无事（~~犯懒~~），把CS144的lab记一下（目前lab4待调试，但是时间被占用了，后面看情况填坑吧QAQ）

前置知识建议学下MIT6.081，good to have

<!--more-->

# lab0

lab0主要是环境配置和热身，我这里使用的是Ubuntu20的系统，由于CS144先前的版本已经从Github删除，使用的是[Kangyupl的存档](https://gitee.com/kangyupl/sponge)，配置教程可以参考：[CS144 实验环境配置](https://doraemonzzz.com/2021/12/12/2021-12-12-CS144-%E5%AE%9E%E9%AA%8C%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE/#%E8%B0%83%E8%AF%95)，编译也可以看[Doraemonzzz](https://doraemonzzz.com/tags/CS144/)的整理

### webget.cc

```c++
#include <cstdlib>
#include <iostream>

using namespace std;

void get_URL(const string &host, const string &path) {
    TCPSocket sock{};
    sock.connect(Address(host,"http"));
    sock.write("GET "+path+" HTTP/1.1\r\nHost: "+host+"\r\n\r\n");
    sock.shutdown(SHUT_WR);
    while(!sock.eof()){
        cout<<sock.read();
    }
    sock.close();
    return;
}

int main(int argc, char *argv[]) {
    try {
        if (argc <= 0) {
            abort();  // For sticklers: don't try to access argv[0] if argc <= 0.
        }

        // The program takes two command-line arguments: the hostname and "path" part of the URL.
        // Print the usage message unless there are these two arguments (plus the program name
        // itself, so arg count = 3 in total).
        if (argc != 3) {
            cerr << "Usage: " << argv[0] << " HOST PATH\n";
            cerr << "\tExample: " << argv[0] << " stanford.edu /class/cs144\n";
            return EXIT_FAILURE;
        }

        // Get the command-line arguments.
        const string host = argv[1];
        const string path = argv[2];

        // Call the student-written function.
        get_URL(host, path);
    } catch (const exception &e) {
        cerr << e.what() << "\n";
        return EXIT_FAILURE;
    }

    return EXIT_SUCCESS;
}
```

### byte_stream.hh

```c++
#ifndef SPONGE_LIBSPONGE_BYTE_STREAM_HH
#define SPONGE_LIBSPONGE_BYTE_STREAM_HH

#include "util/buffer.hh"

#include <cstddef>
#include <cstdint>
#include <string>
#include <utility>

//! \brief An in-order byte stream.

//! Bytes are written on the "input" side and read from the "output"
//! side.  The byte stream is finite: the writer can end the input,
//! and then no more bytes can be written.
class ByteStream {
  private:
    // Your code here -- add private members as necessary.
    BufferList _buffer = {};
    size_t _capacity = 0;
    size_t _read_count = 0;
    size_t _write_count = 0;
    bool _input_ended_flag = false;
    bool _error = false;  //!< Flag indicating that the stream suffered an error.

  public:
    //! Construct a stream with room for `capacity` bytes.
    ByteStream(const size_t capacity);

    //! \name "Input" interface for the writer
    //!@{

    //! Write a string of bytes into the stream. Write as many
    //! as will fit, and return how many were written.
    //! \returns the number of bytes accepted into the stream
    size_t write(const std::string &data);

    //! \returns the number of additional bytes that the stream has space for
    size_t remaining_capacity() const;

    //! Signal that the byte stream has reached its ending
    void end_input();

    //! Indicate that the stream suffered an error.
    void set_error() { _error = true; }
    //!@}

    //! \name "Output" interface for the reader
    //!@{

    //! Peek at next "len" bytes of the stream
    //! \returns a string
    std::string peek_output(const size_t len) const;

    //! Remove bytes from the buffer
    void pop_output(const size_t len);

    //! Read (i.e., copy and then pop) the next "len" bytes of the stream
    //! \returns a vector of bytes read
    std::string read(const size_t len) {
        const auto ret = peek_output(len);
        pop_output(len);
        return ret;
    }

    //! \returns `true` if the stream input has ended
    bool input_ended() const;

    //! \returns `true` if the stream has suffered an error
    bool error() const { return _error; }

    //! \returns the maximum amount that can currently be read from the stream
    size_t buffer_size() const;

    //! \returns `true` if the buffer is empty
    bool buffer_empty() const;

    //! \returns `true` if the output has reached the ending
    bool eof() const;
    //!@}

    //  ! \name General accounting
    //!@{

    //! Total number of bytes written
    size_t bytes_written() const;

    //! Total number of bytes popped
    size_t bytes_read() const;
    //!@}
};

#endif  // SPONGE_LIBSPONGE_BYTE_STREAM_HH
```

### byte_stream.cc

```c++
#include "byte_stream.hh"

#include <algorithm>
#include <iterator>
#include <sstream>
#include <stdexcept>

// Dummy implementation of a flow-controlled in-memory byte stream.

// For Lab 0, please replace with a real implementation that passes the
// automated checks run by `make check_lab0`.

// You will need to add private members to the class declaration in `byte_stream.hh`

template <typename... Targs>
void DUMMY_CODE(Targs &&... /* unused */) {}

using namespace std;

ByteStream::ByteStream(const size_t capacity) : _capacity(capacity) {}

size_t ByteStream::write(const string &data) {
    size_t len = data.length();
    if (len > _capacity - _buffer.size()) {
        len = _capacity - _buffer.size();
    }
    _write_count += len;
    string s;
    s.assign(data.begin(), data.begin() + len);
    _buffer.append(BufferList(move(s)));
    return len;
}

//! \param[in] len bytes will be copied from the output side of the buffer
string ByteStream::peek_output(const size_t len) const {
    size_t length = len;
    if (length > _buffer.size()) {
        length = _buffer.size();
    }
    string s = _buffer.concatenate();
    return string().assign(s.begin(), s.begin() + length);
}

//! \param[in] len bytes will be removed from the output side of the buffer
void ByteStream::pop_output(const size_t len) {
    size_t length = len;
    if (length > _buffer.size()) {
        length = _buffer.size();
    }
    _read_count += length;
    _buffer.remove_prefix(length);
    return;
}

void ByteStream::end_input() { _input_ended_flag = true; }

bool ByteStream::input_ended() const { return _input_ended_flag; }

size_t ByteStream::buffer_size() const { return _buffer.size(); }

bool ByteStream::buffer_empty() const { return _buffer.size() == 0; }

bool ByteStream::eof() const { return buffer_empty() && input_ended(); }

size_t ByteStream::bytes_written() const { return _write_count; }

size_t ByteStream::bytes_read() const { return _read_count; }

size_t ByteStream::remaining_capacity() const { return _capacity - _buffer.size(); }
```



# lab1

## 配置

其他lab同理

```shell
git checkout -b lab1-startercode
git fetch
git merge origin/lab1-startercode
cd build
make -j4 && make check_lab1
```

## 实验结构

这幅图完整的说明了CS144 这门实验的结构：

![CS144](https://raw.githubusercontent.com/Ar-Gas/Ar-Gas.github.io/main/photo/image-20211105142904316.png)

其中， `ByteStream` 是我们已经在 Lab0 中实现完成的。

我们将在接下来的实验中分别实现：

- Lab1 `StreamReassembler`：实现一个流重组器，一个将字节流的字串或者小段按照正确顺序来拼接回连续字节流的模块
- Lab2 `TCPReceiver`：实现入站字节流的TCP部分：这涉及到考虑 TCP 如何表示流中每个字节的位置（称为“序列号”）。 TCPReceiver 负责告诉发送方 (a) 它已经能够成功组装多少入站字节流（这称为“确认”）以及 (b) 发送方现在还可以发送多少字节（“流量控制”）。
- Lab3 `TCPSender`：实现出站字节流的TCP部分：当发送方怀疑其传输的数据段在传输过程中丢失并且从未到达接收方时，应该如何反应？什么时候应该重试并重新传输丢失的分段？
- Lab4 `TCPConnection`: 结合之前的工作来创建一个有效的 TCP 实现。最后我们可以使用这个 TCP 实现来和真实世界的服务器进行通信。

> 该实验引导我们以模块化的方式构建一个 TCP 实现。

流重组器在 TCP 起到了相当重要的作用。迫于网络环境的限制，TCP 发送者会将数据切割成一个个小段的数据分批发送。但这就可能带来一些新的问题：数据在网络中传输时可能丢失、重排、多次重传等等。而TCP接收者就必须通过流重组器，将接收到的**这些重排重传等等的数据包**重新组装成新的连续字节流。

![流重组器](https://raw.githubusercontent.com/Ar-Gas/Ar-Gas.github.io/main/photo/image-20211107124153476.png)

## 环境配置

```bash
git clone https://gitee.com/kangyupl/sponge
git checkout -b lab1-startercode origin/lab1-startercode
mkdir build && cd build
cmake ..
make format
make -j4 && make check_lab1 
```

## 接口

```c++
// Construct a `StreamReassembler` that will store up to `capacity` bytes.
// 构造一个“StreamReassembler”，最多可存储“capacity”字节。
StreamReassembler(const size_t capacity);
// Receive a substring and write any newly contiguous bytes into the stream,
// while staying within the memory limits of the `capacity`. Bytes that would
// exceed the capacity are silently discarded.
//
// `data`: the substring
// `index` indicates the index (place in sequence) of the first byte in `data`
// `eof`: the last byte of this substring will be the last byte in the entire stream
// 接收子字符串并将任何新的连续字节写入流中， 
// 同时保持在“容量”的内存限制内。 
// 超出容量的字节将被默默丢弃。 
//
// `data`：子字符串 
// `index` 表示 `data` 中第一个字节的索引（按顺序排列） 
// `eof`：该子字符串的最后一个字节将是该子字符串中的最后一个字节整个流
void push_substring(const string &data, const uint64_t index, const bool eof);
// Access the reassembled ByteStream (your code from Lab 0)
// 访问重新组装的字节流（来自lab 0 的代码）
ByteStream &stream_out();
// The number of bytes in the substrings stored but not yet reassembled
// 已存储但尚未重组的子字符串中的字节数
size_t unassembled_bytes() const;
// Is the internal state empty (other than the output stream)?
// 内部状态是否为空（除了输出流）？
bool empty() const;
```

## StreamReassembler实现

自己写的过不了（，这里参考[@huangrt01](https://github.com/huangrt01/TCP-Lab/tree/master)

### stream_reassembler.hh

```c++
//! \brief type of unassembled elm
class typeUnassembled {
  public:	
    size_t _index;
    std::string _data;
    typeUnassembled(size_t index, std::string data) : _index(index), _data(data){};
    bool operator <(typeUnassembled t)const{
      return _index < t._index;
    }
}; 


//! \brief A class that assembles a series of excerpts from a byte stream (possibly out of order,
//! possibly overlapping) into an in-order byte stream.
class StreamReassembler {
  private:
    // Your code here -- add private members as necessary.
    /// @brief 
    std::set<typeUnassembled> _Unassembled;
    size_t _firstUnassembled;
    size_t _nUnassembled;
    bool _eof;
    ByteStream _output;  //!< The reassembled in-order byte stream
    size_t _capacity;    //!< The maximum number of bytes

    int merge_substring(size_t & index, std::string &data, size_t index2, const std::string &data2);
  public:
    //! \brief Construct a `StreamReassembler` that will store up to `capacity` bytes.
    //! \note This capacity limits both the bytes that have been reassembled,
    //! and those that have not yet been reassembled.
    StreamReassembler(const size_t capacity);

    //! \brief Receives a substring and writes any newly contiguous bytes into the stream.
    //!
    //! If accepting all the data would overflow the `capacity` of this
    //! `StreamReassembler`, then only the part of the data that fits will be
    //! accepted. If the substring is only partially accepted, then the `eof`
    //! will be disregarded.
    //!
    //! \param data the string being added
    //! \param index the index of the first byte in `data`
    //! \param eof whether or not this segment ends with the end of the stream
    void push_substring(const std::string &data, const uint64_t index, const bool eof);

    //! \name Access the reassembled byte stream
    //!@{
    const ByteStream &stream_out() const { return _output; }
    ByteStream &stream_out() { return _output; }
    //!@}

    //! The number of bytes in the substrings stored but not yet reassembled
    //!
    //! \note If the byte at a particular index has been submitted twice, it
    //! should only be counted once for the purpose of this function.
    size_t unassembled_bytes() const;

    //! \brief Is the internal state empty (other than the output stream)?
    //! \returns `true` if no substrings are waiting to be assembled
    bool empty() const;
};

#endif  // SPONGE_LIBSPONGE_STREAM_REASSEMBLER_HH
```

### stream_reassembler.cc

```c++
StreamReassembler::StreamReassembler(const size_t capacity) : _Unassembled(), _firstUnassembled(0),
                                     _nUnassembled(0), _eof(0), _output(capacity), _capacity(capacity){}

//! \details This function accepts a substring (aka a segment) of bytes,
//! possibly out-of-order, from the logical stream, and assembles any newly
//! contiguous substrings and writes them into the output stream in order.
void StreamReassembler::push_substring(const std::string &data, const uint64_t index, const bool eof){
    // discard or not
    if(data.empty() || index + data.size() <= _firstUnassembled){
        _eof = _eof | eof;
        if (empty() && _eof){
            _output.end_input();
        }
        return;
    }
    // max of capacity
    size_t firstUnacceptbled = _firstUnassembled + _capacity - _output.buffer_size(); 
    // optimization
    size_t resIndex = index;
    size_t beginIndex = 0;
    size_t endIndex = data.size();
    // Delete the redundant ones in front
    if (index < _firstUnassembled){
        resIndex = _firstUnassembled;
        beginIndex = _firstUnassembled - index;
    }
    // Delete the redundant ones in back
    if (index + data.size() >= firstUnacceptbled) {
        endIndex = firstUnacceptbled - index;
    }
    auto resData = std::string(data.begin() + beginIndex, data.begin() + endIndex);
    // Merge with front part
    //           | resData |
    //         <----|iter|
    auto iter = _Unassembled.lower_bound(typeUnassembled(resIndex, ""));
    while (iter != _Unassembled.begin()){
        // resIndex > _firstUnassembled
        if (iter == _Unassembled.end()){
            iter--;
        }
        if (size_t deleteNum = 
                merge_substring(resIndex, resData, (*iter)._index, (*iter)._data)){
                    _nUnassembled -= deleteNum;
                    if (iter != _Unassembled.begin()){
                        _Unassembled.erase(iter--);
                    }else {
                        _Unassembled.erase(iter);
                        break;
                    }
                } else {
                    break;
                }           
    }
    // Merge with back part
    //         ｜resData |
    //          | iter ... | --->
    iter = _Unassembled.lower_bound(typeUnassembled(resIndex, ""));
    while (iter != _Unassembled.end()){
        if (size_t deleteNum =
                   merge_substring(resIndex, resData, (*iter)._index, (*iter)._data)){
                        _Unassembled.erase(iter++);
                        _nUnassembled -= deleteNum;
        } else {
            break;
        }
    }

    // match
    if (resIndex == _firstUnassembled) {
        size_t wSize = _output.write(resData);
        if ((wSize == resData.size()) && eof){
            _eof = true;
            _output.end_input();
        }
        _firstUnassembled += wSize;
    }
    // eof
    if (resData.empty()){
        _eof = _eof | eof;
    }else if (resIndex > _firstUnassembled) {
        _eof = _eof | eof;
        _Unassembled.insert(typeUnassembled(resIndex, resData));
        _nUnassembled += resData.size();
    }
    
    if (empty() && _eof) {
        _output.end_input();
    }
    return;
}

int StreamReassembler::merge_substring(size_t &index, std::string &data, size_t index2, const std::string &data2) {
    // return value:
    // > 0: successfully merge, return the overlapped size
    // 0: fail to merge
    size_t l1 = index, r1 = l1 + data.size() - 1;
    size_t l2 = index2, r2 = l2 + data2.size() - 1;
    if (l2 > r1 + 1 || l1 > r2 + 1) {
        return 0;
    }
    index = min(l1, l2);
    size_t deleteNum = data2.size();
    if (l1 <= l2) {
        if (r2 > r1) {
            data += std::string(data2.begin() + r1 - l2 + 1, data2.end());
        }
    } else {
        if (r1 > r2) {
            data = data2 + std::string(data.begin() + r2 - l1 + 1, data.end());
        } else {
            data = data2;
        }
    }
    return deleteNum;
}

size_t StreamReassembler::unassembled_bytes() const { return _nUnassembled; }

bool StreamReassembler::empty() const { return _nUnassembled == 0; }
```

# lab2

## 序列号转换

WrappingInt32包装了一个无符号32位（循环序号）转无符号64位的方法，转换中用checkpoint做判断避免一对多

```c++
// 随机的isn + 转为32位的n绝对序列号（从0开始）
WrappingInt32 wrap(uint64_t n, WrappingInt32 isn) {
    return WrappingInt32{isn + static_cast<uint32_t>(n)};
}

uint64_t unwrap(WrappingInt32 n, WrappingInt32 isn, uint64_t checkpoint) {
    uint32_t offset = n - wrap(checkpoint, isn);
    uint64_t ret = checkpoint + offset;
    // 取距离checkpoint最近的值，因此需要判断是否左移ret
    // 注意位置不够左移的情形！！！
    if (offset >= (1u << 31) && ret >= (1ul << 32)) {//确保在无符号32位整数的范围内
        ret -= (1ul << 32);
    }
    return ret;
}
```

## TCPReceiver

![TCP报文结构](https://raw.githubusercontent.com/Ar-Gas/Ar-Gas.github.io/main/photo/图像9.png)



![image-20211107122822566](https://raw.githubusercontent.com/Ar-Gas/Ar-Gas.github.io/main/photo/image-20211107122822566.png)

对于 TCPReceiver 来说，除了错误状态以外，它一共有3种状态，分别是：

- LISTEN：等待 SYN 包的到来。若在 SYN 包到来前就有其他数据到来，则**必须丢弃**。
- SYN_RECV：获取到了 SYN 包，此时可以正常的接收数据包
- FIN_RECV：获取到了 FIN 包，此时务必终止 ByteStream 数据流的输入。

在每次 TCPReceiver 接收到数据包时，我们该如何知道当前接收者处于什么状态呢？可以通过以下方式快速判断：

- 当 isn 还没设置时，肯定是 LISTEN 状态
- 当 ByteStream.input_ended()，则肯定是 FIN_RECV 状态
- 其他情况下，是 SYN_RECV 状态

tcp_receiver.hh

```c++
class TCPReceiver {
    //! Our data structure for re-assembling bytes.
    StreamReassembler _reassembler;
    bool _syn_received;
    bool _fin_received;
    WrappingInt32 _isn;
    WrappingInt32 _ackno;
    uint64_t _checkpoint;
    //! The maximum number of bytes we'll store.
    size_t _capacity;

  public:
    //! \brief Construct a TCP receiver
    //!
    //! \param capacity the maximum number of bytes that the receiver will
    //!                 store in its buffers at any give time.
    TCPReceiver(const size_t capacity)
        : _reassembler(capacity), _syn_received(0), _fin_received(0), _isn(0), _ackno(0), _checkpoint(0) {}
    
    ......
    
};
```

tcp_receiver.cc

```c++
bool TCPReceiver::segment_received(const TCPSegment &seg) {
    const bool old_syn_received = _syn_received, old_fin_received = _fin_received;
    const TCPHeader &hdr = seg.header();

    if (!hdr.syn && !_syn_received){
        return false;
    }
    if (_reassembler.eof() && hdr.fin){
        return false;
    }

    if (!_syn_received){
        _isn = hdr.seqno;
        _syn_received = true;
    }
    uint64_t win_start = unwrap(*ackno(), _isn, _checkpoint);
    uint64_t win_size = window_size() ? window_size() : 1;
    uint64_t win_end = win_start + win_size - 1;

    uint64_t seq_start = unwrap(hdr.seqno, _isn, _checkpoint);
    uint64_t seq_size = seg.length_in_sequence_space();
    seq_size = (seq_size) ? seq_size : 1;
    uint64_t seq_end = seq_start + seq_size - 1;

    uint64_t payload_size = seg.payload().size();
    payload_size = (payload_size) ? payload_size : 1;
    uint64_t payload_end = seq_start + payload_size - 1;

    bool inbound = (seq_start >= win_start && seq_start <= win_end) || (payload_end >= win_start && seq_end <= win_end);
    if (inbound){
        _reassembler.push_substring(seg.payload().copy(), seq_start - 1, hdr.fin);  //忽视syn，所以减1
        _checkpoint = _reassembler.unassembled_bytes();
    }

    if (hdr.fin && !_fin_received) {
        _fin_received = 1;
        // if flags = SF and payload_size = 0, we need to end_input() the stream manually
        if (hdr.syn && seg.length_in_sequence_space() == 2) {
            stream_out().end_input();
        }
    }

    bool fin_finished = _fin_received && (_reassembler.unassembled_bytes() == 0);
    _ackno = wrap(_reassembler.unassembled_bytes() + 1 + fin_finished, _isn);  //+1因为bytestream不给syn标号

    // second syn or fin will be rejected
    if ((inbound) || (hdr.fin && !old_fin_received) || (hdr.syn && !old_syn_received)) {
        return true;
    }
    return false;
}

optional<WrappingInt32> TCPReceiver::ackno() const {
    if (!_syn_received) {
        return nullopt;
    } else {
        return {_ackno};
    }
}

size_t TCPReceiver::window_size() const { return stream_out().remaining_capacity(); }
```

# lab3

## TCPsender

![TCPsender](https://raw.githubusercontent.com/Ar-Gas/Ar-Gas.github.io/main/photo/eb8abd73-a0e0-4310-a59f-17b5505114f6.png)

### 1.TCPSender 功能

TCP Sender 负责

### 2. 如何检测丢包

TCP 使用**超时重传机制**。TCPSender 除了将原始数据流分解成众多 TCP 报文并发送以外，它还会追踪每个**已发送报文**（已被发送但还未被接收）的发送时间。如果某些**已发送报文**太久没有被接收方确认（即接收方接收到对应的 ackno），则该数据包必须**重传**。

需要注意的是，**接收方返回的 ackno 并不一定对应着发送方返回的 seqno**（也不和 seqno 有算数关系），这是因为发送的数据可能会因为内存问题，被接收方截断。

接收方确认某个报文，指的是该报文的**所有字节索引**都已被确认。这意味着如果该报文只有**部分被确认**，则不能说明该报文已被完全确认。

TCP 的超时机制比较麻烦，这是因为超时机制直接影响到应用程序从远程服务器上读取数据的响应时间，以及影响到网络拥堵的程度。以下是实现 TCPSender 时需要注意的一些点：

- 每隔几毫秒，TCPSender的 tick 函数将会被调用，其参数声明了过去的时间。这是 TCPSender 唯一能调用的超时时间相关函数。因为直接调用 clock 或者 time 将会导致测试套件不可用。

- TCPSender 在构造时会被给予一个**重传超时时间 RTO**的初始值。RTO 是在重新发送未完成 TCP 段之前需要等待的毫秒数。RTO值将会随着时间的流逝（或者更应该说是网络环境的变化）而变化，但**初始的RTO**将始终不变。

- 在 TCPSender 中，我们需要实现一个**重传计时器**。该计时器将会在 RTO 结束时进行一些操作。

- 当每次发送**包含数据**的数据包时，都需要启动重传计时器，并让它在 RTO 毫秒后超时。若所有**发送中报文**均被确认，则终止重传计时器。

- 如果重传计时器超时，则需要进行以下几步（稍微有点麻烦）

  - 重传尚未被 TCP 接收方完全确认的最早报文（即最低 ackno所对应的报文）。这一步需要我们将**发送中的报文数据**保存至一个新的数据结构中，这样才可以追踪正处于发送状态的数据。

  - 如果接收者的 window size 不为 0，即可以正常接收数据，则

    - 跟踪**连续重传次数**。过多的重传次数可能意味着网络的中断，需要立即停止重传。
    - 将RTO的值设置为先前的两倍，以降低较差网络环境的重传速度，以避免加深网络环境的拥堵。
    - 重置并重启重传计时器。

    > **接收者 window size 为 0 的情况**将在下面说明。

- 当接收者给发送者一个确认成功接收新数据的 ack 包时（absolute ack seqno 比之前接收到的 ackno 更大）：

  - 将 RTO 设置回初始值
  - 如果发送方存在尚未完成的数据，则重新启动重传定时器
  - 将**连续重传计数**清零。

### 代码

tcp_sender.h

```c++
#ifndef SPONGE_LIBSPONGE_TCP_SENDER_HH
#define SPONGE_LIBSPONGE_TCP_SENDER_HH

#include "byte_stream.hh"
#include "tcp_config.hh"
#include "tcp_segment.hh"
#include "wrapping_integers.hh"

#include <cassert>
#include <functional>
#include <queue>
#include <set>

//! \brief The "sender" part of a TCP implementation.

//! Accepts a ByteStream, divides it up into segments and sends the
//! segments, keeps track of which segments are still in-flight,
//! maintains the Retransmission Timer, and retransmits in-flight
//! segments if the retransmission timer expires.
class TCPRetransmissionTimer {
  public:
    //! retransmission timer for the connection
    unsigned int _initial_RTO;

    //! retransmission timeout
    unsigned int _RTO;

    //! timeout
    unsigned int _TO;

    //! state of the timer, 1:open, 0:close
    bool _open;

    //! Initialize a TCP retransmission timer
    TCPRetransmissionTimer(const uint16_t retx_timeout)
        : _initial_RTO(retx_timeout), _RTO(retx_timeout), _TO(0), _open(true) {}

    //! state of the timer
    bool open() { return _open; }

    //! start the timer
    void start() {
        _open = true;
        _TO = 0;
    }

    //! close the timer
    void close() {
        _open = false;
        _TO = 0;
    }

    //! tick
    bool tick(size_t &ms_since_last_tick) {
        if (!open()) {
            return false;
        }
        if (ms_since_last_tick > _RTO - _TO) {
            ms_since_last_tick -= (_RTO - _TO);
            _TO = _RTO;
        } else {
            _TO += ms_since_last_tick;
            ms_since_last_tick = 0;
        }
        if (_TO >= _RTO) {
            _TO = 0;
            return true;  // the retransmission timer has expired.
        }
        return false;
    }
};

class TCPSender {
  private:
    //! our initial sequence number, the number for our SYN.
    WrappingInt32 _isn;

    //! outbound queue of segments that the TCPSender wants sent
    std::queue<TCPSegment> _segments_out;

    //! outstanding segments that the TCPSender may resend
    std::queue<TCPSegment> _segments_outstanding;

    //! bytes in flight
    uint64_t _nBytes_inflight;

    //！ last ackno
    uint64_t _recv_ackno;

    //! TCP retransmission timer
    TCPRetransmissionTimer _timer;

    //! notify the window size
    uint16_t _window_size;

    //! consecutive retransmissions
    unsigned int _consecutive_retransmissions;

    //! outgoing stream of bytes that have not yet been sent
    ByteStream _stream;

    //! the (absolute) sequence number for the next byte to be sent
    uint64_t _next_seqno;

    //! the flag of SYN sent
    bool _syn_sent;

    //! the flag of FIN sent
    bool _fin_sent;

    //! \brief Generate an non-empty segment
    void send_non_empty_segment(TCPSegment &seg);

  public:
    //! Initialize a TCPSender
    TCPSender(const size_t capacity = TCPConfig::DEFAULT_CAPACITY,
              const uint16_t retx_timeout = TCPConfig::TIMEOUT_DFLT,
              const std::optional<WrappingInt32> fixed_isn = {});

    //! \name "Input" interface for the writer
    //!@{
    ByteStream &stream_in() { return _stream; }
    const ByteStream &stream_in() const { return _stream; }
    //!@}

    //! \name Methods that can cause the TCPSender to send a segment
    //!@{

    //! \brief A new acknowledgment was received
    bool ack_received(const WrappingInt32 ackno, const uint16_t window_size);

    //! \brief Generate an empty-payload segment (useful for creating empty ACK segments)
    void send_empty_segment();

    //! \brief create and send segments to fill as much of the window as possible
    void fill_window();

    //! \brief Notifies the TCPSender of the passage of time
    void tick(const size_t ms_since_last_tick);
    //!@}

    //! \name Accessors
    //!@{

    //! \brief How many sequence numbers are occupied by segments sent but not yet acknowledged?
    //! \note count is in "sequence space," i.e. SYN and FIN each count for one byte
    //! (see TCPSegment::length_in_sequence_space())
    size_t bytes_in_flight() const;

    //! \brief Number of consecutive retransmissions that have occurred in a row
    unsigned int consecutive_retransmissions() const;

    //! \brief TCPSegments that the TCPSender has enqueued for transmission.
    //! \note These must be dequeued and sent by the TCPConnection,
    //! which will need to fill in the fields that are set by the TCPReceiver
    //! (ackno and window size) before sending.
    std::queue<TCPSegment> &segments_out() { return _segments_out; }
    //!@}

    //! \name What is the next sequence number? (used for testing)
    //!@{

    //! \brief absolute seqno for the next byte to be sent
    uint64_t next_seqno_absolute() const { return _next_seqno; }

    //! \brief relative seqno for the next byte to be sent
    WrappingInt32 next_seqno() const { return wrap(_next_seqno, _isn); }
    //!@}

    bool syn_sent() const { return _syn_sent; }

    bool fin_sent() const { return _fin_sent; }
};

#endif  // SPONGE_LIBSPONGE_TCP_SENDER_HH
```

tcp_sender.cc

```c++
#include "tcp_sender.hh"

#include "tcp_config.hh"

#include <random>

// Dummy implementation of a TCP sender

// For Lab 3, please replace with a real implementation that passes the
// automated checks run by `make check_lab3`.

template <typename... Targs>
void DUMMY_CODE(Targs &&... /* unused */) {}

using namespace std;

//! \param[in] capacity the capacity of the outgoing byte stream
//! \param[in] retx_timeout the initial amount of time to wait before retransmitting the oldest outstanding segment
//! \param[in] fixed_isn the Initial Sequence Number to use, if set (otherwise uses a random ISN)
TCPSender::TCPSender(const size_t capacity, const uint16_t retx_timeout, const std::optional<WrappingInt32> fixed_isn)
    : _isn(fixed_isn.value_or(WrappingInt32{random_device()()}))
    , _segments_out{}
    , _segments_outstanding{}
    , _nBytes_inflight(0)
    , _recv_ackno(0)
    , _timer{retx_timeout}
    , _window_size(1)
    , _consecutive_retransmissions{0}
    , _stream(capacity)
    , _next_seqno(0)
    , _syn_sent(0)
    , _fin_sent(0) {}

uint64_t TCPSender::bytes_in_flight() const { return _nBytes_inflight; }

void TCPSender::fill_window() {
    assert(!_stream.error());
    TCPSegment seg;
    if (_next_seqno == 0) {
        // state is CLOSE, need to send SYN
        seg.header().syn = 1;
        _syn_sent = 1;
        send_non_empty_segment(seg);
        return;
    } else if (_next_seqno == _nBytes_inflight) {
        // state is SYN SENT, don't send SYN
        return;
    }

    // zero window probing
    uint16_t window_size = _window_size ? _window_size : 1;
    uint64_t remaining;
    while ((remaining = static_cast<uint64_t>(window_size) + (_recv_ackno - _next_seqno))) {
        // FIN flag occupies space in window
        TCPSegment new_seg;
        if (_stream.eof() && !_fin_sent) {
            new_seg.header().fin = 1;
            _fin_sent = 1;
            send_non_empty_segment(new_seg);
            return;
        } else if (_stream.eof()) {
            return;
        }
        size_t size = min(static_cast<size_t>(remaining), TCPConfig::MAX_PAYLOAD_SIZE);
        new_seg.payload() = Buffer(_stream.read(size));
        if (new_seg.length_in_sequence_space() < window_size && _stream.eof()) {  // piggy-back FIN
            new_seg.header().fin = 1;
            _fin_sent = 1;
        }
        if (new_seg.length_in_sequence_space() == 0) {
            return;
        }
        send_non_empty_segment(new_seg);
    }
}

//! \param ackno The remote receiver's ackno (acknowledgment number)
//! \param window_size The remote receiver's advertised window size
//! \returns `false` if the ackno appears invalid (acknowledges something the TCPSender hasn't sent yet)
bool TCPSender::ack_received(const WrappingInt32 ackno, const uint16_t window_size) {
    if (ackno - next_seqno() > 0) {
        return false;
    }
    //如果window_size为0，需要记录下来，"zero window probing", 影响tick()和fill_window()的行为
    _window_size = window_size;
    uint64_t abs_ackno = unwrap(ackno, _isn, _recv_ackno);
    if (abs_ackno <= _recv_ackno) {
        return true;
    }

    _recv_ackno = abs_ackno;
    // acknowledges the successful receipt of new data
    _timer._RTO = _timer._initial_RTO;
    _timer._TO = 0;
    _consecutive_retransmissions = 0;

    // deque fully-acknowledged segments
    while (!_segments_outstanding.empty()) {
        const auto &seg = _segments_outstanding.front();
        if (ackno - seg.header().seqno >= static_cast<int32_t>(seg.length_in_sequence_space())) {
            _nBytes_inflight -= seg.length_in_sequence_space();
            _segments_outstanding.pop();
        } else {
            break;
        }
    }

    // fill the window
    fill_window();

    // any outstanding segment, restart the timer.
    // [RFC6298](5.3)
    if (!_segments_outstanding.empty()) {
        _timer.start();
    }
    return true;
}

//! \param[in] ms_since_last_tick the number of milliseconds since the last call to this method
void TCPSender::tick(const size_t ms_since_last_tick) {
    size_t time_left = ms_since_last_tick;
    if (_timer.tick(time_left)) {
        // Notice: remove fill_the_window() here to fix the test fsm_retx_relaxed
        // timer has expired, retransmit at most ONE outstanding segment
        if (!_segments_outstanding.empty()) {
            // retransmit the outstanding segment with the lowest sequence number
            _segments_out.push(_segments_outstanding.front());
            if (_window_size) {
                _consecutive_retransmissions++;
                _timer._RTO *= 2;  // double the RTO, exponential backoff, it slows down retransmissions on lousy
                                   // networks to avoid further gumming up the works
            }
            if (!_timer.open()) {  //[RFC6298](5.1)
                _timer.start();
            }
            if (syn_sent() && (_next_seqno == _nBytes_inflight) && (_timer._RTO < 3000)) {
                _timer._RTO = 3000;  // SYN_SENT, [RFC6298](5.7)
            }
        }
        if (_segments_outstanding.empty()) {
            _timer.close();
        }
    }
}

unsigned int TCPSender::consecutive_retransmissions() const { return _consecutive_retransmissions; }

void TCPSender::send_empty_segment() {
    TCPSegment seg;
    seg.header().seqno = wrap(_next_seqno, _isn);
    _segments_out.push(seg);
}

void TCPSender::send_non_empty_segment(TCPSegment &seg) {
    seg.header().seqno = wrap(_next_seqno, _isn);

    _next_seqno += seg.length_in_sequence_space();
    _nBytes_inflight += seg.length_in_sequence_space();
    // std::cerr << "send non empty: " << seg.header().to_string() << "length:" << seg.length_in_sequence_space() <<
    // endl << endl;
    _segments_out.push(seg);
    _segments_outstanding.push(seg);

    // [RFC6298]:(5.1)
    if (!_timer.open()) {
        _timer.start();
    }
}
```























# lab4

## 示意图

![img](https://github.com/Doraemonzzz/md-photo/blob/master/CS144/Lab/Lab4/6.jpg?raw=true)















# 参考资料

[1].[译\] RFC 1180：朴素 TCP/IP 教程（1991）](http://arthurchiao.art/blog/rfc1180-a-tcp-ip-tutorial-zh/)

[2].[计算机网络笔记-1](https://kiprey.github.io/2021/05/cnatda-1/#%E7%AE%80%E4%BB%8B)

[3].[详解 TCP 超时与重传机制](https://zhuanlan.zhihu.com/p/101702312)

[4].[Breadcrumbs/TCP-Lab](https://github.com/huangrt01/TCP-Lab)（代码质量非常高，极具学习价值！）

[5].「[RFC6298](https://link.zhihu.com/?target=https%3A//tools.ietf.org/html/rfc6298)」

[6].[【计算机网络】Stanford CS144 Lab Assignments 学习笔记 ](https://www.cnblogs.com/kangyupl/p/stanford_cs144_labs.html)

[7].[CS144 Lab & 翻译 - Doraemonzzz](https://doraemonzzz.com/tags/CS144/)

[8].[CS144 Lab Assignments - 手写TCP - LAB4](https://segmentfault.com/a/1190000039220243)
