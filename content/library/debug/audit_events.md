+++
title = "审计事件表"
date = 2024-11-15T21:06:32+08:00
weight = 1
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文：[https://docs.python.org/zh-cn/3.13/library/audit_events.html](https://docs.python.org/zh-cn/3.13/library/audit_events.html)
>
> 收录该文档的时间：`2024-11-15T21:06:32+08:00`

# 审计事件表

​	下表包含了在整个 CPython 运行时和标准库中由 [`sys.audit()`]({{< ref "/library/python/sys#sys.audit" >}}) 或 [`PySys_Audit()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_Audit) 调用所引发的全部事件。 这些调用是在 3.8 或更高版本中添加的 (参见 [**PEP 578**](https://peps.python.org/pep-0578/))。

​	请参阅 [`sys.addaudithook()`]({{< ref "/library/python/sys#sys.addaudithook" >}}) 和 [`PySys_AddAuditHook()`](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_AddAuditHook) 了解有关处理这些事件的详细信息。

**CPython 实现细节：** 此表是根据 CPython 文档生成的，可能无法表示其他实现所引发的事件。 请参阅你的运行时专属的文档了解实际引发的事件。

| Audit event                      | Arguments                                                    | References                                                   |
| :------------------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| _thread.start_new_thread         | `function`, `args`, `kwargs`                                 | [[1\]]({{< ref "/library/concurrency/_thread#start_new_thread" >}}) |
| array.__new__                    | `typecode`, `initializer`                                    | [[1\]]({{< ref "/library/datatypes/array#array.array" >}}) |
| builtins.breakpoint              | `breakpointhook`                                             | [[1\]]({{< ref "/library/functions#breakpoint" >}}) |
| builtins.id                      | `id`                                                         | [[1\]]({{< ref "/library/functions#id" >}}) |
| builtins.input                   | `prompt`                                                     | [[1\]]({{< ref "/library/functions#input" >}}) |
| builtins.input/result            | `result`                                                     | [[1\]]({{< ref "/library/functions#input" >}}) |
| code.__new__                     | `code`, `filename`, `name`, `argcount`, `posonlyargcount`, `kwonlyargcount`, `nlocals`, `stacksize`, `flags` | [[1\]]({{< ref "/library/datatypes/types#types.CodeType" >}}) |
| compile                          | `source`, `filename`                                         | [[1\]]({{< ref "/library/functions#compile" >}}) |
| cpython.PyInterpreterState_Clear |                                                              | [[1\]]({{< ref "/c_api/init#c.PyInterpreterState_Clear" >}}) |
| cpython.PyInterpreterState_New   |                                                              | [[1\]]({{< ref "/c_api/init#c.PyInterpreterState_New" >}}) |
| cpython._PySys_ClearAuditHooks   |                                                              | [[1\]]({{< ref "/c_api/init#c.Py_FinalizeEx" >}}) |
| cpython.run_command              | `command`                                                    | [[1\]]({{< ref "/using/cmdline#cmdoption-c" >}}) |
| cpython.run_file                 | `filename`                                                   | [[1\]]({{< ref "/using/cmdline#audit_event_cpython_run_file_0" >}}) |
| cpython.run_interactivehook      | `hook`                                                       | [[1\]]({{< ref "/library/python/sys#sys.__interactivehook__" >}}) |
| cpython.run_module               | `module-name`                                                | [[1\]]({{< ref "/using/cmdline#cmdoption-m" >}}) |
| cpython.run_startup              | `filename`                                                   | [[1\]]({{< ref "/using/cmdline#envvar-PYTHONSTARTUP" >}}) |
| cpython.run_stdin                |                                                              | [[1\]]({{< ref "/library/ipc/asyncio#audit_event_cpython_run_stdin_0" >}})[[2\]]({{< ref "/using/cmdline#audit_event_cpython_run_stdin_0" >}})[[3\]]({{< ref "/using/cmdline#audit_event_cpython_run_stdin_1" >}}) |
| ctypes.addressof                 | `obj`                                                        | [[1\]]({{< ref "/library/allos/ctypes#ctypes.addressof" >}}) |
| ctypes.call_function             | `func_pointer`, `arguments`                                  | [[1\]]({{< ref "/library/allos/ctypes#foreign-functions" >}}) |
| ctypes.cdata                     | `address`                                                    | [[1\]]({{< ref "/library/allos/ctypes#ctypes._CData.from_address" >}}) |
| ctypes.cdata/buffer              | `pointer`, `size`, `offset`                                  | [[1\]]({{< ref "/library/allos/ctypes#ctypes._CData.from_buffer" >}})[[2\]]({{< ref "/library/allos/ctypes#ctypes._CData.from_buffer_copy" >}}) |
| ctypes.create_string_buffer      | `init`, `size`                                               | [[1\]]({{< ref "/library/allos/ctypes#ctypes.create_string_buffer" >}}) |
| ctypes.create_unicode_buffer     | `init`, `size`                                               | [[1\]]({{< ref "/library/allos/ctypes#ctypes.create_unicode_buffer" >}}) |
| ctypes.dlopen                    | `name`                                                       | [[1\]]({{< ref "/library/allos/ctypes#ctypes.LibraryLoader" >}}) |
| ctypes.dlsym                     | `library`, `name`                                            | [[1\]]({{< ref "/library/allos/ctypes#ctypes.LibraryLoader" >}}) |
| ctypes.dlsym/handle              | `handle`, `name`                                             | [[1\]]({{< ref "/library/allos/ctypes#ctypes.LibraryLoader" >}}) |
| ctypes.get_errno                 |                                                              | [[1\]]({{< ref "/library/allos/ctypes#ctypes.get_errno" >}}) |
| ctypes.get_last_error            |                                                              | [[1\]]({{< ref "/library/allos/ctypes#ctypes.get_last_error" >}}) |
| ctypes.set_errno                 | `errno`                                                      | [[1\]]({{< ref "/library/allos/ctypes#ctypes.set_errno" >}}) |
| ctypes.set_exception             | `code`                                                       | [[1\]]({{< ref "/library/allos/ctypes#foreign-functions" >}}) |
| ctypes.set_last_error            | `error`                                                      | [[1\]]({{< ref "/library/allos/ctypes#ctypes.set_last_error" >}}) |
| ctypes.string_at                 | `ptr`, `size`                                                | [[1\]]({{< ref "/library/allos/ctypes#ctypes.string_at" >}}) |
| ctypes.wstring_at                | `ptr`, `size`                                                | [[1\]]({{< ref "/library/allos/ctypes#ctypes.wstring_at" >}}) |
| ensurepip.bootstrap              | `root`                                                       | [[1\]]({{< ref "/library/distribution/ensurepip#ensurepip.bootstrap" >}}) |
| exec                             | `code_object`                                                | [[1\]]({{< ref "/library/functions#eval" >}})[[2\]]({{< ref "/library/functions#exec" >}}) |
| fcntl.fcntl                      | `fd`, `cmd`, `arg`                                           | [[1\]]({{< ref "/library/unix/fcntl#fcntl.fcntl" >}}) |
| fcntl.flock                      | `fd`, `operation`                                            | [[1\]]({{< ref "/library/unix/fcntl#fcntl.flock" >}}) |
| fcntl.ioctl                      | `fd`, `request`, `arg`                                       | [[1\]]({{< ref "/library/unix/fcntl#fcntl.ioctl" >}}) |
| fcntl.lockf                      | `fd`, `cmd`, `len`, `start`, `whence`                        | [[1\]]({{< ref "/library/unix/fcntl#fcntl.lockf" >}}) |
| ftplib.connect                   | `self`, `host`, `port`                                       | [[1\]]({{< ref "/library/internet/ftplib#ftplib.FTP.connect" >}}) |
| ftplib.sendcmd                   | `self`, `cmd`                                                | [[1\]]({{< ref "/library/internet/ftplib#ftplib.FTP.sendcmd" >}})[[2\]]({{< ref "/library/internet/ftplib#ftplib.FTP.voidcmd" >}}) |
| function.__new__                 | `code`                                                       | [[1\]]({{< ref "/library/datatypes/types#types.FunctionType" >}}) |
| gc.get_objects                   | `generation`                                                 | [[1\]]({{< ref "/library/python/gc#gc.get_objects" >}}) |
| gc.get_referents                 | `objs`                                                       | [[1\]]({{< ref "/library/python/gc#gc.get_referents" >}}) |
| gc.get_referrers                 | `objs`                                                       | [[1\]]({{< ref "/library/python/gc#gc.get_referrers" >}}) |
| glob.glob                        | `pathname`, `recursive`                                      | [[1\]]({{< ref "/library/filesys/glob#glob.glob" >}})[[2\]]({{< ref "/library/filesys/glob#glob.iglob" >}}) |
| glob.glob/2                      | `pathname`, `recursive`, `root_dir`, `dir_fd`                | [[1\]]({{< ref "/library/filesys/glob#glob.glob" >}})[[2\]]({{< ref "/library/filesys/glob#glob.iglob" >}}) |
| http.client.connect              | `self`, `host`, `port`                                       | [[1\]]({{< ref "/library/internet/http_client#http.client.HTTPConnection.connect" >}}) |
| http.client.send                 | `self`, `data`                                               | [[1\]]({{< ref "/library/internet/http_client#http.client.HTTPConnection.send" >}}) |
| imaplib.open                     | `self`, `host`, `port`                                       | [[1\]]({{< ref "/library/internet/imaplib#imaplib.IMAP4.open" >}}) |
| imaplib.send                     | `self`, `data`                                               | [[1\]]({{< ref "/library/internet/imaplib#imaplib.IMAP4.send" >}}) |
| import                           | `module`, `filename`, `sys.path`, `sys.meta_path`, `sys.path_hooks` | [[1\]]({{< ref "/reference/simple_stmts#import" >}}) |
| marshal.dumps                    | `value`, `version`                                           | [[1\]]({{< ref "/library/persistence/marshal#marshal.dump" >}}) |
| marshal.load                     |                                                              | [[1\]]({{< ref "/library/persistence/marshal#marshal.load" >}}) |
| marshal.loads                    | `bytes`                                                      | [[1\]]({{< ref "/library/persistence/marshal#marshal.load" >}}) |
| mmap.__new__                     | `fileno`, `length`, `access`, `offset`                       | [[1\]]({{< ref "/library/ipc/mmap#mmap.mmap" >}}) |
| msvcrt.get_osfhandle             | `fd`                                                         | [[1\]]({{< ref "/library/windows/msvcrt#msvcrt.get_osfhandle" >}}) |
| msvcrt.locking                   | `fd`, `mode`, `nbytes`                                       | [[1\]]({{< ref "/library/windows/msvcrt#msvcrt.locking" >}}) |
| msvcrt.open_osfhandle            | `handle`, `flags`                                            | [[1\]]({{< ref "/library/windows/msvcrt#msvcrt.open_osfhandle" >}}) |
| object.__delattr__               | `obj`, `name`                                                | [[1\]]({{< ref "/reference/datamodel#object.__delattr__" >}}) |
| object.__getattr__               | `obj`, `name`                                                | [[1\]]({{< ref "/reference/datamodel#object.__getattribute__" >}}) |
| object.__setattr__               | `obj`, `name`, `value`                                       | [[1\]]({{< ref "/reference/datamodel#object.__setattr__" >}}) |
| open                             | `path`, `mode`, `flags`                                      | [[1\]]({{< ref "/library/functions#open" >}})[[2\]]({{< ref "/library/allos/io#io.open" >}})[[3\]]({{< ref "/library/allos/os#os.open" >}}) |
| os.add_dll_directory             | `path`                                                       | [[1\]]({{< ref "/library/allos/os#os.add_dll_directory" >}}) |
| os.chdir                         | `path`                                                       | [[1\]]({{< ref "/library/allos/os#os.chdir" >}})[[2\]]({{< ref "/library/allos/os#os.fchdir" >}}) |
| os.chflags                       | `path`, `flags`                                              | [[1\]]({{< ref "/library/allos/os#os.chflags" >}})[[2\]]({{< ref "/library/allos/os#os.lchflags" >}}) |
| os.chmod                         | `path`, `mode`, `dir_fd`                                     | [[1\]]({{< ref "/library/allos/os#os.chmod" >}})[[2\]]({{< ref "/library/allos/os#os.fchmod" >}})[[3\]]({{< ref "/library/allos/os#os.lchmod" >}}) |
| os.chown                         | `path`, `uid`, `gid`, `dir_fd`                               | [[1\]]({{< ref "/library/allos/os#os.chown" >}})[[2\]]({{< ref "/library/allos/os#os.fchown" >}})[[3\]]({{< ref "/library/allos/os#os.lchown" >}}) |
| os.exec                          | `path`, `args`, `env`                                        | [[1\]]({{< ref "/library/allos/os#os.execl" >}}) |
| os.fork                          |                                                              | [[1\]]({{< ref "/library/allos/os#os.fork" >}}) |
| os.forkpty                       |                                                              | [[1\]]({{< ref "/library/allos/os#os.forkpty" >}}) |
| os.fwalk                         | `top`, `topdown`, `onerror`, `follow_symlinks`, `dir_fd`     | [[1\]]({{< ref "/library/allos/os#os.fwalk" >}}) |
| os.getxattr                      | `path`, `attribute`                                          | [[1\]]({{< ref "/library/allos/os#os.getxattr" >}}) |
| os.kill                          | `pid`, `sig`                                                 | [[1\]]({{< ref "/library/allos/os#os.kill" >}}) |
| os.killpg                        | `pgid`, `sig`                                                | [[1\]]({{< ref "/library/allos/os#os.killpg" >}}) |
| os.link                          | `src`, `dst`, `src_dir_fd`, `dst_dir_fd`                     | [[1\]]({{< ref "/library/allos/os#os.link" >}}) |
| os.listdir                       | `path`                                                       | [[1\]]({{< ref "/library/allos/os#os.listdir" >}}) |
| os.listdrives                    |                                                              | [[1\]]({{< ref "/library/allos/os#os.listdrives" >}}) |
| os.listmounts                    | `volume`                                                     | [[1\]]({{< ref "/library/allos/os#os.listmounts" >}}) |
| os.listvolumes                   |                                                              | [[1\]]({{< ref "/library/allos/os#os.listvolumes" >}}) |
| os.listxattr                     | `path`                                                       | [[1\]]({{< ref "/library/allos/os#os.listxattr" >}}) |
| os.lockf                         | `fd`, `cmd`, `len`                                           | [[1\]]({{< ref "/library/allos/os#os.lockf" >}}) |
| os.mkdir                         | `path`, `mode`, `dir_fd`                                     | [[1\]]({{< ref "/library/allos/os#os.makedirs" >}})[[2\]]({{< ref "/library/allos/os#os.mkdir" >}}) |
| os.posix_spawn                   | `path`, `argv`, `env`                                        | [[1\]]({{< ref "/library/allos/os#os.posix_spawn" >}})[[2\]]({{< ref "/library/allos/os#os.posix_spawnp" >}}) |
| os.putenv                        | `key`, `value`                                               | [[1\]]({{< ref "/library/allos/os#os.putenv" >}}) |
| os.remove                        | `path`, `dir_fd`                                             | [[1\]]({{< ref "/library/allos/os#os.remove" >}})[[2\]]({{< ref "/library/allos/os#os.removedirs" >}})[[3\]]({{< ref "/library/allos/os#os.unlink" >}}) |
| os.removexattr                   | `path`, `attribute`                                          | [[1\]]({{< ref "/library/allos/os#os.removexattr" >}}) |
| os.rename                        | `src`, `dst`, `src_dir_fd`, `dst_dir_fd`                     | [[1\]]({{< ref "/library/allos/os#os.rename" >}})[[2\]]({{< ref "/library/allos/os#os.renames" >}})[[3\]]({{< ref "/library/allos/os#os.replace" >}}) |
| os.rmdir                         | `path`, `dir_fd`                                             | [[1\]]({{< ref "/library/allos/os#os.rmdir" >}}) |
| os.scandir                       | `path`                                                       | [[1\]]({{< ref "/library/allos/os#os.scandir" >}}) |
| os.setxattr                      | `path`, `attribute`, `value`, `flags`                        | [[1\]]({{< ref "/library/allos/os#os.setxattr" >}}) |
| os.spawn                         | `mode`, `path`, `args`, `env`                                | [[1\]]({{< ref "/library/allos/os#os.spawnl" >}}) |
| os.startfile                     | `path`, `operation`                                          | [[1\]]({{< ref "/library/allos/os#os.startfile" >}}) |
| os.startfile/2                   | `path`, `operation`, `arguments`, `cwd`, `show_cmd`          | [[1\]]({{< ref "/library/allos/os#os.startfile" >}}) |
| os.symlink                       | `src`, `dst`, `dir_fd`                                       | [[1\]]({{< ref "/library/allos/os#os.symlink" >}}) |
| os.system                        | `command`                                                    | [[1\]]({{< ref "/library/allos/os#os.system" >}}) |
| os.truncate                      | `fd`, `length`                                               | [[1\]]({{< ref "/library/allos/os#os.ftruncate" >}})[[2\]]({{< ref "/library/allos/os#os.truncate" >}}) |
| os.unsetenv                      | `key`                                                        | [[1\]]({{< ref "/library/allos/os#os.unsetenv" >}}) |
| os.utime                         | `path`, `times`, `ns`, `dir_fd`                              | [[1\]]({{< ref "/library/allos/os#os.utime" >}}) |
| os.walk                          | `top`, `topdown`, `onerror`, `followlinks`                   | [[1\]]({{< ref "/library/allos/os#os.walk" >}}) |
| pathlib.Path.glob                | `self`, `pattern`                                            | [[1\]]({{< ref "/library/filesys/pathlib#pathlib.Path.glob" >}}) |
| pathlib.Path.rglob               | `self`, `pattern`                                            | [[1\]]({{< ref "/library/filesys/pathlib#pathlib.Path.rglob" >}}) |
| pdb.Pdb                          |                                                              | [[1\]]({{< ref "/library/debug/pdb#pdb.Pdb" >}}) |
| pickle.find_class                | `module`, `name`                                             | [[1\]]({{< ref "/library/persistence/pickle#pickle.Unpickler.find_class" >}}) |
| poplib.connect                   | `self`, `host`, `port`                                       | [[1\]]({{< ref "/library/internet/poplib#poplib.POP3" >}})[[2\]]({{< ref "/library/internet/poplib#poplib.POP3_SSL" >}}) |
| poplib.putline                   | `self`, `line`                                               | [[1\]]({{< ref "/library/internet/poplib#poplib.POP3" >}})[[2\]]({{< ref "/library/internet/poplib#poplib.POP3_SSL" >}}) |
| pty.spawn                        | `argv`                                                       | [[1\]]({{< ref "/library/unix/pty#pty.spawn" >}}) |
| resource.prlimit                 | `pid`, `resource`, `limits`                                  | [[1\]]({{< ref "/library/unix/resource#resource.prlimit" >}}) |
| resource.setrlimit               | `resource`, `limits`                                         | [[1\]]({{< ref "/library/unix/resource#resource.setrlimit" >}}) |
| setopencodehook                  |                                                              | [[1\]](https://docs.python.org/zh-cn/3.13/c-api/file.html#c.PyFile_SetOpenCodeHook) |
| shutil.chown                     | `path`, `user`, `group`                                      | [[1\]]({{< ref "/library/filesys/shutil#shutil.chown" >}}) |
| shutil.copyfile                  | `src`, `dst`                                                 | [[1\]]({{< ref "/library/filesys/shutil#shutil.copy" >}})[[2\]]({{< ref "/library/filesys/shutil#shutil.copy2" >}})[[3\]]({{< ref "/library/filesys/shutil#shutil.copyfile" >}}) |
| shutil.copymode                  | `src`, `dst`                                                 | [[1\]]({{< ref "/library/filesys/shutil#shutil.copy" >}})[[2\]]({{< ref "/library/filesys/shutil#shutil.copymode" >}}) |
| shutil.copystat                  | `src`, `dst`                                                 | [[1\]]({{< ref "/library/filesys/shutil#shutil.copy2" >}})[[2\]]({{< ref "/library/filesys/shutil#shutil.copystat" >}}) |
| shutil.copytree                  | `src`, `dst`                                                 | [[1\]]({{< ref "/library/filesys/shutil#shutil.copytree" >}}) |
| shutil.make_archive              | `base_name`, `format`, `root_dir`, `base_dir`                | [[1\]]({{< ref "/library/filesys/shutil#shutil.make_archive" >}}) |
| shutil.move                      | `src`, `dst`                                                 | [[1\]]({{< ref "/library/filesys/shutil#shutil.move" >}}) |
| shutil.rmtree                    | `path`, `dir_fd`                                             | [[1\]]({{< ref "/library/filesys/shutil#shutil.rmtree" >}}) |
| shutil.unpack_archive            | `filename`, `extract_dir`, `format`                          | [[1\]]({{< ref "/library/filesys/shutil#shutil.unpack_archive" >}}) |
| signal.pthread_kill              | `thread_id`, `signalnum`                                     | [[1\]]({{< ref "/library/ipc/signal#signal.pthread_kill" >}}) |
| smtplib.connect                  | `self`, `host`, `port`                                       | [[1\]]({{< ref "/library/internet/smtplib#smtplib.SMTP.connect" >}}) |
| smtplib.send                     | `self`, `data`                                               | [[1\]]({{< ref "/library/internet/smtplib#smtplib.SMTP" >}}) |
| socket.__new__                   | `self`, `family`, `type`, `protocol`                         | [[1\]]({{< ref "/library/ipc/socket#socket.socket" >}}) |
| socket.bind                      | `self`, `address`                                            | [[1\]]({{< ref "/library/ipc/socket#socket.socket.bind" >}}) |
| socket.connect                   | `self`, `address`                                            | [[1\]]({{< ref "/library/ipc/socket#socket.socket.connect" >}})[[2\]]({{< ref "/library/ipc/socket#socket.socket.connect_ex" >}}) |
| socket.getaddrinfo               | `host`, `port`, `family`, `type`, `protocol`                 | [[1\]]({{< ref "/library/ipc/socket#socket.getaddrinfo" >}}) |
| socket.gethostbyaddr             | `ip_address`                                                 | [[1\]]({{< ref "/library/ipc/socket#socket.gethostbyaddr" >}}) |
| socket.gethostbyname             | `hostname`                                                   | [[1\]]({{< ref "/library/ipc/socket#socket.gethostbyname" >}})[[2\]]({{< ref "/library/ipc/socket#socket.gethostbyname_ex" >}}) |
| socket.gethostname               |                                                              | [[1\]]({{< ref "/library/ipc/socket#socket.gethostname" >}}) |
| socket.getnameinfo               | `sockaddr`                                                   | [[1\]]({{< ref "/library/ipc/socket#socket.getnameinfo" >}}) |
| socket.getservbyname             | `servicename`, `protocolname`                                | [[1\]]({{< ref "/library/ipc/socket#socket.getservbyname" >}}) |
| socket.getservbyport             | `port`, `protocolname`                                       | [[1\]]({{< ref "/library/ipc/socket#socket.getservbyport" >}}) |
| socket.sendmsg                   | `self`, `address`                                            | [[1\]]({{< ref "/library/ipc/socket#socket.socket.sendmsg" >}}) |
| socket.sendto                    | `self`, `address`                                            | [[1\]]({{< ref "/library/ipc/socket#socket.socket.sendto" >}}) |
| socket.sethostname               | `name`                                                       | [[1\]]({{< ref "/library/ipc/socket#socket.sethostname" >}}) |
| sqlite3.connect                  | `database`                                                   | [[1\]]({{< ref "/library/persistence/sqlite3#sqlite3.connect" >}}) |
| sqlite3.connect/handle           | `connection_handle`                                          | [[1\]]({{< ref "/library/persistence/sqlite3#sqlite3.connect" >}}) |
| sqlite3.enable_load_extension    | `connection`, `enabled`                                      | [[1\]]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.enable_load_extension" >}}) |
| sqlite3.load_extension           | `connection`, `path`                                         | [[1\]]({{< ref "/library/persistence/sqlite3#sqlite3.Connection.load_extension" >}}) |
| subprocess.Popen                 | `executable`, `args`, `cwd`, `env`                           | [[1\]]({{< ref "/library/concurrency/subprocess#subprocess.Popen" >}}) |
| sys._current_exceptions          |                                                              | [[1\]]({{< ref "/library/python/sys#sys._current_exceptions" >}}) |
| sys._current_frames              |                                                              | [[1\]]({{< ref "/library/python/sys#sys._current_frames" >}}) |
| sys._getframe                    | `frame`                                                      | [[1\]]({{< ref "/library/python/sys#sys._getframe" >}}) |
| sys._getframemodulename          | `depth`                                                      | [[1\]]({{< ref "/library/python/sys#sys._getframemodulename" >}}) |
| sys.addaudithook                 |                                                              | [[1\]](https://docs.python.org/zh-cn/3.13/c-api/sys.html#c.PySys_AddAuditHook)[[2\]]({{< ref "/library/python/sys#sys.addaudithook" >}}) |
| sys.excepthook                   | `hook`, `type`, `value`, `traceback`                         | [[1\]]({{< ref "/library/python/sys#sys.excepthook" >}}) |
| sys.set_asyncgen_hooks_finalizer |                                                              | [[1\]]({{< ref "/library/python/sys#sys.set_asyncgen_hooks" >}}) |
| sys.set_asyncgen_hooks_firstiter |                                                              | [[1\]]({{< ref "/library/python/sys#sys.set_asyncgen_hooks" >}}) |
| sys.setprofile                   |                                                              | [[1\]]({{< ref "/library/python/sys#sys.setprofile" >}}) |
| sys.settrace                     |                                                              | [[1\]]({{< ref "/library/python/sys#sys.settrace" >}}) |
| sys.unraisablehook               | `hook`, `unraisable`                                         | [[1\]]({{< ref "/library/python/sys#sys.unraisablehook" >}}) |
| syslog.closelog                  |                                                              | [[1\]]({{< ref "/library/unix/syslog#syslog.closelog" >}}) |
| syslog.openlog                   | `ident`, `logoption`, `facility`                             | [[1\]]({{< ref "/library/unix/syslog#syslog.openlog" >}}) |
| syslog.setlogmask                | `maskpri`                                                    | [[1\]]({{< ref "/library/unix/syslog#syslog.setlogmask" >}}) |
| syslog.syslog                    | `priority`, `message`                                        | [[1\]]({{< ref "/library/unix/syslog#syslog.syslog" >}}) |
| tempfile.mkdtemp                 | `fullpath`                                                   | [[1\]]({{< ref "/library/filesys/tempfile#tempfile.TemporaryDirectory" >}})[[2\]]({{< ref "/library/filesys/tempfile#tempfile.mkdtemp" >}}) |
| tempfile.mkstemp                 | `fullpath`                                                   | [[1\]]({{< ref "/library/filesys/tempfile#tempfile.NamedTemporaryFile" >}})[[2\]]({{< ref "/library/filesys/tempfile#tempfile.TemporaryFile" >}})[[3\]]({{< ref "/library/filesys/tempfile#tempfile.mkstemp" >}}) |
| time.sleep                       | `secs`                                                       | [[1\]]({{< ref "/library/allos/time#audit_event_time_sleep_0" >}}) |
| urllib.Request                   | `fullurl`, `data`, `headers`, `method`                       | [[1\]]({{< ref "/library/internet/urllib/urllib_request#urllib.request.urlopen" >}}) |
| webbrowser.open                  | `url`                                                        | [[1\]]({{< ref "/library/internet/webbrowser#webbrowser.open" >}}) |
| winreg.ConnectRegistry           | `computer_name`, `key`                                       | [[1\]]({{< ref "/library/windows/winreg#winreg.ConnectRegistry" >}}) |
| winreg.CreateKey                 | `key`, `sub_key`, `access`                                   | [[1\]]({{< ref "/library/windows/winreg#winreg.CreateKey" >}})[[2\]]({{< ref "/library/windows/winreg#winreg.CreateKeyEx" >}}) |
| winreg.DeleteKey                 | `key`, `sub_key`, `access`                                   | [[1\]]({{< ref "/library/windows/winreg#winreg.DeleteKey" >}})[[2\]]({{< ref "/library/windows/winreg#winreg.DeleteKeyEx" >}}) |
| winreg.DeleteValue               | `key`, `value`                                               | [[1\]]({{< ref "/library/windows/winreg#winreg.DeleteValue" >}}) |
| winreg.DisableReflectionKey      | `key`                                                        | [[1\]]({{< ref "/library/windows/winreg#winreg.DisableReflectionKey" >}}) |
| winreg.EnableReflectionKey       | `key`                                                        | [[1\]]({{< ref "/library/windows/winreg#winreg.EnableReflectionKey" >}}) |
| winreg.EnumKey                   | `key`, `index`                                               | [[1\]]({{< ref "/library/windows/winreg#winreg.EnumKey" >}}) |
| winreg.EnumValue                 | `key`, `index`                                               | [[1\]]({{< ref "/library/windows/winreg#winreg.EnumValue" >}}) |
| winreg.ExpandEnvironmentStrings  | `str`                                                        | [[1\]]({{< ref "/library/windows/winreg#winreg.ExpandEnvironmentStrings" >}}) |
| winreg.LoadKey                   | `key`, `sub_key`, `file_name`                                | [[1\]]({{< ref "/library/windows/winreg#winreg.LoadKey" >}}) |
| winreg.OpenKey                   | `key`, `sub_key`, `access`                                   | [[1\]]({{< ref "/library/windows/winreg#winreg.OpenKey" >}}) |
| winreg.OpenKey/result            | `key`                                                        | [[1\]]({{< ref "/library/windows/winreg#winreg.CreateKey" >}})[[2\]]({{< ref "/library/windows/winreg#winreg.CreateKeyEx" >}})[[3\]]({{< ref "/library/windows/winreg#winreg.OpenKey" >}}) |
| winreg.PyHKEY.Detach             | `key`                                                        | [[1\]]({{< ref "/library/windows/winreg#winreg.PyHKEY.Detach" >}}) |
| winreg.QueryInfoKey              | `key`                                                        | [[1\]]({{< ref "/library/windows/winreg#winreg.QueryInfoKey" >}}) |
| winreg.QueryReflectionKey        | `key`                                                        | [[1\]]({{< ref "/library/windows/winreg#winreg.QueryReflectionKey" >}}) |
| winreg.QueryValue                | `key`, `sub_key`, `value_name`                               | [[1\]]({{< ref "/library/windows/winreg#winreg.QueryValue" >}})[[2\]]({{< ref "/library/windows/winreg#winreg.QueryValueEx" >}}) |
| winreg.SaveKey                   | `key`, `file_name`                                           | [[1\]]({{< ref "/library/windows/winreg#winreg.SaveKey" >}}) |
| winreg.SetValue                  | `key`, `sub_key`, `type`, `value`                            | [[1\]]({{< ref "/library/windows/winreg#winreg.SetValue" >}})[[2\]]({{< ref "/library/windows/winreg#winreg.SetValueEx" >}}) |

​	下列事件只在内部被引发，而不会回应任何 CPython 公共 API:

| 审计事件                 | 实参                                                         |
| :----------------------- | :----------------------------------------------------------- |
| _winapi.CreateFile       | `file_name`, `desired_access`, `share_mode`, `creation_disposition`, `flags_and_attributes` |
| _winapi.CreateJunction   | `src_path`, `dst_path`                                       |
| _winapi.CreateNamedPipe  | `name`, `open_mode`, `pipe_mode`                             |
| _winapi.CreatePipe       |                                                              |
| _winapi.CreateProcess    | `application_name`, `command_line`, `current_directory`      |
| _winapi.OpenProcess      | `process_id`, `desired_access`                               |
| _winapi.TerminateProcess | `handle`, `exit_code`                                        |
| ctypes.PyObj_FromPtr     | `obj`                                                        |
