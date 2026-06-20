# Common FTP Client Commands

| Command | Description |
|----------|-------------|
| `!` | Execute a local shell command. |
| `$` | Execute a previously defined macro. |
| `account` | Send account information to the FTP server. |
| `append` | Append data to a remote file. |
| `ascii` | Set ASCII transfer mode. |
| `bell` | Ring a bell when a transfer completes. |
| `binary` | Set binary transfer mode. |
| `bye` | Disconnect from the FTP server and exit. |
| `case` | Toggle filename case conversion during downloads. |
| `cdup` | Move to the parent directory on the remote server. |
| `chmod` | Change permissions of a remote file. |
| `close` | Close the current FTP connection. |
| `connect` | Connect to an FTP server. |
| `cr` | Toggle carriage return stripping. |
| `debug` | Enable or disable debug output. |
| `delete` | Delete a remote file. |
| `dir` | Display a detailed directory listing. |
| `disconnect` | Disconnect from the server. |
| `edit` | Open the local editor. |
| `epsv` | Toggle Extended Passive Mode (EPSV). |
| `epsv4` | Use EPSV for IPv4 connections. |
| `epsv6` | Use EPSV for IPv6 connections. |
| `exit` | Exit the FTP client. |
| `features` | Display server-supported features. |
| `fget` | Download files matching a pattern. |
| `form` | Set the transfer form. |
| `gate` | Control Gate FTP mode. |
| `get` | Download a file from the remote server. |
| `glob` | Toggle wildcard expansion. |
| `hash` | Display hash marks to show transfer progress. |
| `help` | Display the help menu. |
| `idle` | Display or set the idle timeout value. |
| `image` | Alias for binary transfer mode. |
| `lcd` | Change the local working directory. |
| `less` | Display output through a pager. |
| `lpage` | Display local output through a pager. |
| `lpwd` | Show the current local directory. |
| `ls` | Display directory contents. |
| `macdef` | Define a macro. |
| `mdelete` | Delete multiple remote files. |
| `mdir` | Save listings of multiple files to a local file. |
| `mget` | Download multiple files. |
| `mkdir` | Create a remote directory. |
| `mls` | Save multiple file listings locally. |
| `mlsd` | Display machine-readable directory listings. |
| `mlst` | Display detailed information about a file or directory. |
| `modtime` | Show the modification time of a remote file. |
| `more` | Display output page by page. |
| `mput` | Upload multiple files. |
| `mreget` | Resume downloading multiple files. |
| `msend` | Upload multiple files. |
| `newer` | Download only files newer than local copies. |
| `nlist` | Display a simple list of filenames. |
| `nmap` | Configure filename mapping rules. |
| `ntrans` | Configure filename translation rules. |
| `open` | Open an FTP connection. |
| `page` | Display output through a pager. |
| `passive` | Toggle passive mode. |
| `pdir` | Show directory listings through a pager. |
| `pls` | Display listings through a pager. |
| `pmlsd` | Display MLSD output through a pager. |
| `preserve` | Preserve file timestamps during transfers. |
| `progress` | Enable or disable transfer progress display. |
| `prompt` | Toggle interactive prompts. |
| `proxy` | Use a proxy FTP connection. |
| `put` | Upload a file to the remote server. |
| `pwd` | Display the current remote directory. |
| `quit` | Exit the FTP client. |
| `quote` | Send a raw FTP command directly to the server. |
| `rate` | Set transfer rate limits. |
| `rcvbuf` | Set the receive buffer size. |
| `recv` | Download a file from the server. |
| `reget` | Resume an interrupted download. |
| `remopts` | Display or configure remote options. |
| `rename` | Rename a remote file. |
| `reset` | Clear the reply queue. |
| `restart` | Set the restart point for a transfer. |
| `rhelp` | Display help information from the remote server. |
| `rmdir` | Remove a remote directory. |
| `rstatus` | Display the remote server status. |
| `runique` | Save files with unique local filenames. |
| `send` | Upload a file. |
| `sendport` | Toggle usage of the PORT command. |
| `set` | Set FTP client variables. |
| `site` | Send a SITE command to the server. |
| `size` | Display the size of a remote file. |
| `sndbuf` | Set the send buffer size. |
| `status` | Display current client settings and status. |
| `struct` | Set the file structure type. |
| `sunique` | Store uploaded files with unique remote names. |
| `system` | Display the remote operating system type. |
| `tenex` | Set TENEX transfer mode. |
| `throttle` | Limit transfer bandwidth usage. |
| `trace` | Enable or disable packet tracing. |
| `type` | Set the transfer type. |
| `umask` | Display or set the remote umask value. |
| `unset` | Remove a previously set variable. |
| `usage` | Display usage information for a command. |
| `user` | Log in with a username and password. |
| `verbose` | Enable or disable verbose output. |
| `?` | Shortcut for the help command. |

> **Exam Tip:** The most commonly used FTP commands during penetration testing are `ls`, `cd`, `get`, `put`, `pwd`, `mkdir`, `delete`, `mget`, `mput`, `binary`, and `quote`.
