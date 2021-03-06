Name
====

GDB utilities for Nginx, ngx_lua, LuaJIT, and etc.

Table of Contents
=================

* [Name](#name)
* [Status](#status)
* [Synopsis](#synopsis)
* [Description](#description)
* [Commands](#commands)
    * [lbt](#lbt)
    * [lvmst](#lvmst)
* [Prerequisites](#prerequisites)
* [Authors](#authors)
* [Copyright and License](#copyright-and-license)
* [See Also](#see-also)

Status
======

This is still under early development.

Synopsis
========

    # add the path of nginx.py and ngxlua.py modules to the PYTHONPATH env
    (gdb) source luajit21.py
    (gdb) lvmst
    current VM state: C code from intperpreted Lua
    (gdb) lbt
    builtin#166
    builtin#195
    builtin#187
    @/home/agentzh/git/lua-resty-core/lib/resty/core/regex.lua:588
    content_by_lua:10

    (gdb) source ngx-lua.gdb
    (gdb) source luajit20.gdb
    (gdb) lreg L &ngx_http_lua_ctx_tables_key
    <tab: 0x412a68c8>
    (gdb) ltab 0x412a68c8
    tab(4097, 0): {[1]=<tab: 0x4095fc78>, [2]=<tab: 0x4095fc80>, [3]=<tab: 0x4095fc88>,
     [4]=<tab: 0x4095fc90>, [5]=<tab: 0x4095fc98>, [6]=<tab: 0x4095fca0>,
     [7]=<tab: 0x4095fca8>, [8]=<tab: 0x4095fcb0>, [9]=<tab: 0x4095fcb8>,
     [10]=<tab: 0x4095fcc0>, [11]=<tab: 0x4095fcc8>, [12]=<tab: 0x4095fcd0>,
     [13]=<tab: 0x4095fcd8>, [14]=<tab: 0x4095fce0>, [15]=<tab: 0x4095fce8>,
     [16]=<tab: 0x4095fcf0>, [17]=<tab: 0x4095fcf8>, [18]=<tab: 0x4095fd00>,
     ...

    (gdb) lfunc regex.lua 444
    Found function (GCfunc*)0x4025e168 at @/home/agentzh/git/lua-resty-core/lib/resty/core/regex.lua:444

    (gdb) lproto regex.lua 444
    Found proto (GCproto*)0x4025f380 at @/home/agentzh/git/lua-resty-core/lib/resty/core/regex.lua:444

    (gdb) luv (GCfunc*)0x4025e168
    0x4025e168
    Found 23 upvalues.
    upvalue "parse_regex_opts": value=(TValue*)0x4025df60 value_type=func closed=1
    upvalue "type": value=(TValue*)0x4025e1e8 value_type=func closed=1
    upvalue "tostring": value=(TValue*)0x4025e208 value_type=func closed=1
    upvalue "band": value=(TValue*)0x4025dd88 value_type=func closed=1
    upvalue "FLAG_COMPILE_ONCE": value=(TValue*)0x41b8daf8 value_type=number closed=1
    upvalue "regex_cache": value=(TValue*)0x4025df80 value_type=table closed=1
    upvalue "get_string_buf": value=(TValue*)0x4025dfa0 value_type=func closed=1
    upvalue "MAX_ERR_MSG_LEN": value=(TValue*)0x4025dfc0 value_type=number closed=1
    upvalue "C": value=(TValue*)0x41b8da38 value_type=userdata closed=1
    ...

    (gdb) lval (TValue*)0x41b8da38
    udata type: ffi clib
          payload len: 16
          payload ptr: 0x41b81df8
          CLibrary handle: (void*)0x0
          CLibrary cache: (GCtab*)0x41b8d188

    (gdb) lval (TValue*)0x41907840
    type cdata
        cdata object: (GCcdata*)0x41aae7f0
        cdata value pointer: (void*)0x41aae7f8
        ctype object: (CType*)0x40268e70
        ctype size: 1 byte(s)
        ctype type: func
        ctype element name: ngx_http_lua_ffi_destroy_regex

Description
===========

This toolkit provides various gdb extension commands for analyzing core dump files for nginx and/or luajit.

[Back to TOC](#table-of-contents)

Commands
========

The following gdb commands are supported:

[Back to TOC](#table-of-contents)

lbt
---
**syntax:** *lbt*

**syntax:** *lbt &lt;L&gt;*

**file** *luajit21.py*

Fetch the current backtrace from the current running Lua thread(when no argument is given) or the Lua thread specified by the lua_State pointer.

The backtrace format is the same as the one used by the [lj-lua-bt](https://github.com/agentzh/stapxx#lj-lua-bt) tool.

When analyzing ngx_lua's processes, this tool requires the Python module files `nginx.py` and `ngxlua.py` to obtain the global Lua state. You need to add the path of these `.py` files to the `PYTHONPATH` environment variable before starting `gdb`.

Below is an example:

    (gdb) source luajit21.py
    (gdb) lbt
    builtin#166
    builtin#195
    builtin#187
    @/home/agentzh/git/lua-resty-core/lib/resty/core/regex.lua:588
    content_by_lua:10

You can also explicitly specify the Lua thread state you want to analyze, for instance,

    (gdb) lbt 0x169e0e0

Only LuaJIT 2.1 is supported.

[Back to TOC](#table-of-contents)

lvmst
-----
**syntax:** *lvmst*

**syntax:** *lvmst &lt;L&gt;*

**file** *luajit21.py*

Prints out the current state of the LuaJIT 2.1 VM.

Below is an example,

    (gdb) source luajit21.py
    (gdb) lvmst
    current VM state: C code from intperpreted Lua

You can also explicitly specify the lua VM state you want to analyze, for instance,

    (gdb) lvmst 0x169e0e0
    current VM state: C code from intperpreted Lua

You can specify any Lua thread's state in the VM you want to analyze.

[Back to TOC](#table-of-contents)

Prerequisites
=============

You need to enable the debuginfo in your LuaJIT build (and Nginx build if Nginx is involved).

To enable debuginfo in your LuaJIT build, pass the `CCDEBUG=-g` command-line argument to the `make` command, as in

    make CCDEBUG=-g

Also, you are required to use gdb 7.6+ with python support enabled.

[Back to TOC](#table-of-contents)

Authors
=======

* Guanlan Dai.

* Yichun Zhang (agentzh) <agentzh@gmail.com>, CloudFlare Inc.

[Back to TOC](#table-of-contents)

Copyright and License
=====================

This module is licensed under the BSD license.

Copyright (C) 2013-2014, by Guanlan Dai.

Copyright (C) 2013-2014, by Yichun "agentzh" Zhang (章亦春) <agentzh@gmail.com>, CloudFlare Inc.

All rights reserved.

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

* Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

* Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

[Back to TOC](#table-of-contents)

See Also
========

* [Nginx Systemtap Toolkit](https://github.com/agentzh/nginx-systemtap-toolkit)
* Sample tools in the stap++ project: https://github.com/agentzh/stapxx#samples

[Back to TOC](#table-of-contents)
