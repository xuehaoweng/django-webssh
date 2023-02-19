# django-webssh

python-django-webssh

# 后端通过paramiko.SSHClient() 和设备建立后端连接
```
from channels.auth import AuthMiddlewareStack
from channels.routing import ProtocolTypeRouter, URLRouter
```
# 前端通过xterm+websocket

```
import { Terminal } from 'xterm'
import { FitAddon } from 'xterm-addon-fit'

function websocketInit(){
/ webssh_list.length = 0
                //console.log(window.location.search)
                let url = window.location.href
                let getqyinfo = url.split('?')[1]
                let getqys = new URLSearchParams('?' + getqyinfo)
                let id = getqys.get('id')
                let remote_ip = getqys.get('remote_ip')
                var terminal_id = id
                device_id.value = id
                const ws_scheme = window.location.protocol === 'https:' ? 'wss' : 'ws'
                const ws_url =
                    ws_scheme + '://' + window.location.host + '/ws/ssh/' + device_id.value + '/?' + remote_ip
                terminalSocket = new WebSocket(ws_url)
                
                
                nextTick(() => {
                    terminalSocket.onopen = function () {
                        //console.log('连接成功！')
                        let init_width = 9;
                        let init_height = 18;
                        const _width = window.innerWidth
                        const _height = window.innerHeight
                        var cols = Math.floor(_width / init_width)
                        var rows = Math.floor(_height / init_height)
                        term = new Terminal({
                            rendererType: 'canvas', //渲染类型
                            rows: rows, //行数
                            cols: cols, // 不指定行数，自动回车后光标从下一行开始
                            fontSize: 15,
                            scrollback: 500,//终端中的回滚量
                            convertEol: true, //启用时，光标将设置为下一行的开头
                            cursorBlink: true, //光标闪烁
                            disableStdin: false, //是否应禁用输入。
                            cursorStyle: "block", //光标样式
                            theme: {
                                foreground: "#00ff00", //字体
                                background: "black", //背景色#060101
                                cursor: "#00ff00" //设置光标
                            },
                        })
                        term.open(document.getElementById(terminal_id))
                        const fitAddon = new FitAddon() // 全屏插件
                        // term.loadAddon(attachAddon)
                        term.loadAddon(fitAddon)
                        // term.fitAddon.fit()
                        term.focus()
                        var input = 'terminal monitor'

                        if (input.length > 0) {
                            terminalSocket.send(input)
                            terminalSocket.send('\r')
                        }
                        term.onData((val) => {
                            terminalSocket.send(val)
                        })
                        // term.write('terminal monitor')
                        terminalSocket.onmessage = function (evt) {
                            var received_msg = evt.data.toString()
                            //console.log('接受消息：', received_msg)
                            // term.onData((received_msg) => {
                            term.write(received_msg)
                            // })
                        }

                        terminalSocket.onclose = function (event) {
                            //console.log('连接关闭！', event)
                        }
                        terminalSocket.onerror = function (event) {
                            //console.log('连接异常！', event)
                        }
                    }
                })
}
```
