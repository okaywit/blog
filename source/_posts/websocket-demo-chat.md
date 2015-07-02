title: "websocket简单实例-聊天"
date: 2015-06-30 21:10:37
tags: "websocket"
categories: "websocket"
---

通过websocket建立的一个简单聊天工具，web服务器使用tomcat 8.0。

<!-- more -->
##客户端
###ws.js代码
	var Server = {};
	Server.socket = null;
	Server.connect=(function(host){
		Server.socket = new WebSocket(host);
		Server.socket.onopen = function(){
			
		};
		Server.socket.onmessage = function(message){
			console.log(message.data);
		};
		Server.socket.onerror = function(error){

		};
		Server.socket.onclose = function(){
			
		};
	});
	Server.init=function(path){
		Server.connect("ws://localhost:8080/okaywit/"+path);
	};

###chat.html代码
	<!DOCTYPE html>
	<html>
	<head lang="en">
	    <meta charset="UTF-8">
	    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
	    <title>大辉Face</title>
	    <meta name="keywords" content="Polymer,WebSocket,Java,Tomcat,Html5" />
	    <meta name="description" content="免费的 web 技术教程。" />
	</head>
	<!-- 新 Bootstrap 核心 CSS 文件 -->
	<link rel="stylesheet" href="http://cdn.bootcss.com/bootstrap/3.3.4/css/bootstrap.min.css">

	<!-- jQuery文件。务必在bootstrap.min.js 之前引入 -->
	<script src="http://cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>

	<!-- 最新的 Bootstrap 核心 JavaScript 文件 -->
	<script src="http://cdn.bootcss.com/bootstrap/3.3.4/js/bootstrap.min.js"></script>
	<style type="text/css">
	    .IM{
	        text-align: left;
	        overflow:hidden;
	        word-wrap: break-word;
	    }
	    .OM{
	        text-align: right;
	        overflow:hidden;
	        word-wrap: break-word;
	    }
	</style>
	<body class="container-fluid">

	    <div class="row">
	        <div class="col-xs-12 col-md-7 col-md-offset-1" style="" id="M">

	        </div>
	        <div class="col-xs-12 col-md-3"  style="background: gray">

	        </div>
	    </div>
	    <nav class="navbar navbar-default navbar-fixed-bottom">
	        <div class="container">
	            <div class="row">
	                <div class="col-xs-12 col-md-12" style="padding:10px 0px 10px 0px">
	                    <input type="text" class="form-control input-lg" id="message" placeholder="Enter键发送....">
	                </div>
	            </div>

	        </div>
	    </nav>

	<script type="text/javascript" src="js/ws.js"></script>
	<script type="text/javascript">
	    var id;
	    Server.init("chat");
	    Server.socket.onmessage=function(message){
	        var data = eval('('+message.data+')');
	        if(id==undefined){
	            if(data["message"]==""){
	                id=data["id"];
	            }
	            return;
	        }
	        var msg = "<div class='row'><div class='col-xs-6 col-md-6 col-xs-offset-6 col-md-offset-6 OM well'>"+data["message"]+"</div></div>";
	        document.getElementById("M").innerHTML = msg+document.getElementById("M").innerHTML;
	    };

	    document.getElementById("message").addEventListener("keydown",function(e){
	        if(e.keyCode==13){
	            var msg = "<div class='row'><div class='col-xs-6 col-md-6 IM well'>"+this.value+"</div></div>";
	            document.getElementById("M").innerHTML = msg + document.getElementById("M").innerHTML;
	            Server.socket.send(packData(1,id,this.value));
	            this.blur();
	            this.value="";
	        }
	    });

	    function packData(hId,id,msg){
	        return '{"hId":'+hId+',"id":'+id+',"message":"'+msg+'"}';
	    }

	</script>
	</body>
	</html>
##服务端	
###ChatServer.java
	import java.io.IOException;
	import java.util.HashMap;
	import java.util.Iterator;
	import java.util.Map;
	import java.util.Map.Entry;
	import java.util.concurrent.atomic.AtomicLong;
	import javax.websocket.OnClose;
	import javax.websocket.OnError;
	import javax.websocket.OnMessage;
	import javax.websocket.OnOpen;
	import javax.websocket.Session;
	import javax.websocket.server.ServerEndpoint;
	import com.alibaba.fastjson.JSONObject;

	/**
	 * 聊天工具服务器
	 * 
	 * @author Wang Hui
	 */
	@ServerEndpoint("/chat")
	public class ChatServer {
	        //自增ID
	        private static AtomicLong ids = new AtomicLong();
	        //会话缓存
	        private static Map<Long, Session> sessionMap = new HashMap<Long, Session>();

	        @OnOpen
	        public void open(Session session) {
	                long id = ids.getAndIncrement();
	                //缓存
	                sessionMap.put(id, session);
	                try {
	                		//推送id给用户
	                        session.getBasicRemote().sendText(String.format("{\"id\":\"%d\",\"message\":\"%s\"}", id, ""));
	                } catch (IOException e) {
	                        e.printStackTrace();
	                }

	        }

	        @OnMessage
	        public void message(String message) {
	                JSONObject data = JSONObject.parseObject(message);
	                //推送消息
	                for (Iterator<Entry<Long, Session>> it = sessionMap.entrySet().iterator(); it.hasNext();) {
	                        Entry<Long, Session> entry = it.next();
	                        try {
	                                if (entry.getKey() == data.getLongValue("id"))
	                                        continue;

	                                Session session = entry.getValue();
	                                if (session.isOpen())
	                                        session.getBasicRemote().sendText(message);
	                                else
	                                        it.remove();
	                        } catch (IOException e) {
	                                e.printStackTrace();
	                        }
	                }
	        }

	        @OnClose
	        public void close(Session session) {
	                for (Iterator<Entry<Long, Session>> it = sessionMap.entrySet().iterator(); it.hasNext();) {
	                        Entry<Long, Session> entry = it.next();
	                        if (entry.getValue() == session) {
	                                //会话关闭删除
	                                sessionMap.remove(entry.getKey());
	                                break;
	                        }
	                }

	        }

	        @OnError
	        public void error(Throwable t) throws Throwable {
	                t.printStackTrace();
	        }
	}

##jar包下载
[json解析、websocket](http://pan.baidu.com/s/1bn4kAOB "json解析、websocket")
