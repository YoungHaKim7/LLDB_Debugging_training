# Result
- https://lldb.llvm.org/use/map.html

```lldb
# 다음으로 넘기기
(lldb) n

(lldb) b main.rs:5
Breakpoint 4: where = a01_little_endian`a01_little_endian::main + 80 at main.rs:5:2, address = 0x0000000100000904

(lldb) b main
Breakpoint 5: 2 locations.

(lldb) watchpoint set variable int_x

(lldb) script print("Here is some text")
Here is some text

```

- breakpoint 잡기


```lldb
(lldb) b main
Breakpoint 1: 2 locations.
(lldb) b main.rs:3
Breakpoint 2: where = a01_little_endian`a01_little_endian::main::hce7d42c140118505 + 16 at main.rs:3:17, address = 0x00000001000008c4
(lldb) b main.rs:4
Breakpoint 3: 2 locations.
(lldb) b main.rs:5
Breakpoint 4: where = a01_little_endian`a01_little_endian::main::hce7d42c140118505 + 80 at main.rs:5:2, address = 0x0000000100000904

```

- `f` 현재 코드 라인 볼수 있다.
  - https://stackoverflow.com/questions/17480107/lldb-list-source-code

```lldb
(lldb) f
frame #0: 0x00000001000008d0 a01_little_endian`a01_little_endian::main::hce7d42c140118505 at main.rs:4:47
   1   	fn main() {
   2   	    // 4660
   3   	    let int_x = 0x1234;
-> 4   	    println!("int_x (memory address) : {:p}", &int_x);
    	                                              ^
   5   	}

```

- 내가 원하는 변수 보기

```lldb
(lldb) watchpoint set variable int_x
Watchpoint created: Watchpoint 1: addr = 0x16fdfe30c size = 4 state = enabled type = m
    declare @ '/Users/gy-gyoung/my_project/Rust_Lang/9999/2222/5555/LLDB_Debugging_training/Rust_Lang/macOS/a01_little_endian/src/main.rs:3'
    watchpoint spec = 'int_x'
    watchpoint resources:
       #0: addr = 0x16fdfe30c size = 4
Watchpoint 1 hit:

new value: 4660

(lldb) f
frame #0: 0x00000001000008d0 a01_little_endian`a01_little_endian::main::hce7d42c140118505 at main.rs:4:47
   1   	fn main() {
   2   	    // 4660
   3   	    let int_x = 0x1234;
-> 4   	    println!("int_x (memory address) : {:p}", &int_x);
    	                                              ^
   5   	}

(lldb) memory read --gdb-format 4xw 0x16fdfe30c
0x16fdfe30c: 0x00001234 0x000c6010 0x00000001 0x000c4020

(lldb) f
frame #0: 0x00000001000008d0 a01_little_endian`a01_little_endian::main::hce7d42c140118505 at main.rs:4:47
   1   	fn main() {
   2   	    // 4660
   3   	    let int_x = 0x1234;
-> 4   	    println!("int_x (memory address) : {:p}", &int_x);
    	                                              ^
   5   	}
(lldb) disassemble --start-address 0x16fdfe30c --count 20
# 약자
(lldb) di -s 0x16fdfe30c -c 20
    0x16fdfe30c: udf    #0x1234
    0x16fdfe310: <unknown>
    0x16fdfe314: udf    #0x1
    0x16fdfe318: <unknown>
    0x16fdfe31c: udf    #0x1
    0x16fdfe320: udf    #0x0
    0x16fdfe324: udf    #0x0
    0x16fdfe328: udf    #0x4
    0x16fdfe32c: udf    #0x0
    0x16fdfe330: <unknown>
    0x16fdfe334: udf    #0x1
    0x16fdfe338: <unknown>
    0x16fdfe33c: udf    #0x1
    0x16fdfe340: udf    #0x1
    0x16fdfe344: udf    #0x0
    0x16fdfe348: udf    #0x10
    0x16fdfe34c: udf    #0x0
    0x16fdfe350: <unknown>
    0x16fdfe354: udf    #0x1
    0x16fdfe358: fmops  za4.d, p4/m, p6/m, z28.d, z27.d
(lldb)

```

- `r` 누르면 시작됨. (재시작도 같은 명령어)

```lldb
(lldb) r
There is a running process, kill it and restart?: [Y/n] y

- Hook 1 (expr -- argc)

- Hook 2 (expr -- argv)
Process 40053 launched: '/Users/gy-gyoung/my_project/Rust_Lang/9999/2222/5555/LLDB_Debugging_training/Rust_Lang/macOS/a01_little_endian/target/debug/a01_little_endian' (arm64)

- Hook 1 (expr -- argc)

- Hook 2 (expr -- argv)
Process 40053 stopped
* thread #1, queue = 'com.apple.main-thread', stop reason = breakpoint 1.2
    frame #0: 0x0000000100000910 a01_little_endian`main
a01_little_endian`main:
->  0x100000910 <+0>:  stp    x29, x30, [sp, #-0x10]!
    0x100000914 <+4>:  mov    x29, sp
    0x100000918 <+8>:  mov    x2, x1
    0x10000091c <+12>: mov    x8, x0

```

- `c` 를 눌러야 코드랑 같이 보임.

```lldb
(lldb) c
Process 36845 resuming

- Hook 1 (expr -- argc)

- Hook 2 (expr -- argv)
Process 36845 stopped
* thread #1, name = 'main', queue = 'com.apple.main-thread', stop reason = breakpoint 3.1
    frame #0: 0x00000001000008d0 a01_little_endian`a01_little_endian::main::hce7d42c140118505 at main.rs:4:47
   1   	fn main() {
   2   	    // 4660
   3   	    let int_x = 0x1234;
-> 4   	    println!("int_x (memory address) : {:p}", &int_x);
    	                                              ^
   5   	}

```

```lldb
(lldb) thread list
Process 21757 stopped
* thread #1: tid = 0xf521, 0x0000000100000918 a01_little_endian`main + 8, queue = 'com.apple.main-thread', stop reason = instruction step over
(lldb) bt
* thread #1, queue = 'com.apple.main-thread', stop reason = instruction step over
  * frame #0: 0x0000000100000918 a01_little_endian`main + 8
    frame #1: 0x0000000180bf6b98 dyld`start + 6076
```

```lldb
(lldb) watch l
Number of supported hardware watchpoints: 4
No watchpoints currently set.
```

```lldb
(lldb) f 1
frame #1: 0x0000000180bf6b98 dyld`start + 6076
dyld`start:
->  0x180bf6b98 <+6076>: mov    x19, x0
    0x180bf6b9c <+6080>: ldr    x8, [sp, #0x1d0]
    0x180bf6ba0 <+6084>: ldr    x8, [x8, #0x8]
    0x180bf6ba4 <+6088>: add    x0, x8, #0x60
```

- (memory 확인)

```lldb
(lldb) memory read --gdb-format 4xw 0x00000001eecac018
0x1eecac018: 0xeecae930 0x00000001 0x6fdfe5a0 0x00000001
(lldb) memory read `argv[0]`
error: invalid start address expression.
error: address expression "argv[0]" evaluation failed
(lldb) memory region --all
[0x0000000000000000-0x0000000100000000) ---
[0x0000000100000000-0x0000000100004000) r-x __TEXT
Modified memory (dirty) page list provided, 1 entries.
Dirty pages: 0x100000000.
[0x0000000100004000-0x0000000100048000) r-x __TEXT
Modified memory (dirty) page list provided, 0 entries.
[0x0000000100048000-0x000000010004c000) r-- __DATA_CONST
Modified memory (dirty) page list provided, 1 entries.
Dirty pages: 0x100048000.
[0x000000010004c000-0x0000000100050000) rw- __DATA
Modified memory (dirty) page list provided, 1 entries.
Dirty pages: 0x10004c000.
  
```

```lldb
(lldb) register read
General Purpose Registers:
       x19 = 0x00000001eecac0b0  lsl::sAllocatorBuffer
       x20 = 0x00000001eecac018  lsl::sMemoryManagerBuffer
       x21 = 0x000000016fdfe578
       x22 = 0x0fffffff0009d71d
       x23 = 0x00000001eecac018  lsl::sMemoryManagerBuffer
       x24 = 0x00000001eecac150  dyld`lsl::sPoolBytes + 96
       x25 = 0x000000016fdfe6e0
       x26 = 0x0000000000000000
       x27 = 0x0000000000000000
       x28 = 0x0000000000000000
        fp = 0x000000016fdfeb30
        lr = 0x0000000180bf6b98  dyld`start + 6076
        sp = 0x000000016fdfe4f0
        pc = 0x0000000180bf6b98  dyld`start + 6076
20 registers were unavailable.
```


- (lldb) image list
 

```
(lldb) image list
[  0] C677DC8C-1E5E-3481-9B15-D9DF00195895 0x0000000100000000 /Users/gy-gyoung/my_project/Rust_Lang/9999/2222/5555/LLDB_Debugging_training/Rust_Lang/macOS/a01_little_endian/target/debug/a01_little_endian
[  1] 3247E185-CED2-36FF-9E29-47A77C23E004 0x0000000180bf0000 /usr/lib/dyld
[  2] 072C7C60-2B6C-3E07-AA56-FD1750EADC2F 0x000000018f1f3000 /usr/lib/libSystem.B.dylib
[  3] 24CE0D89-4114-30C2-A81A-3DB1F5931CFF 0x0000000180dde000 /usr/lib/system/libdispatch.dylib
[  4] 7254F3F1-A46F-30B6-934A-511B730A8FC7 0x0000000180cf7000 /usr/lib/system/libcorecrypto.dylib
[  5] 2B900F8F-9EDE-3208-AB17-BD1385B5546A 0x000000018f1ed000 /usr/lib/system/libcache.dylib
[  6] 01BCB3F7-AB89-30BD-87EE-91B291EADAE8 0x0000000180f9e000 /usr/lib/system/libdyld.dylib
[  7] E6BC26C2-DED7-3C04-A670-AEDB150BDB1F 0x000000018f1a8000 /usr/li

```

- `(lldb) disassemble --frame`

```
(lldb) disassemble --frame
dyld`start:
    0x180bf53dc <+0>:    pacibsp
    0x180bf53e0 <+4>:    stp    x26, x25, [sp, #-0x50]!
    0x180bf53e4 <+8>:    stp    x24, x23, [sp, #0x10]
    0x180bf53e8 <+12>:   stp    x22, x21, [sp, #0x20]
    0x180bf53ec <+16>:   stp    x20, x19, [sp, #0x30]
    0x180bf53f0 <+20>:   stp    x29, x30, [sp, #0x40]
    0x180bf53f4 <+24>:   add    x29, sp, #0x40
    0x180bf53f8 <+28>:   sub    sp, sp, #0x600
    0x180bf53fc <+32>:   mov    x21, x2
  
```

