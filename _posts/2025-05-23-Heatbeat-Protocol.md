---
layout: post
title: "Heartbeat Message"
date: 2025-05-23
category:
  - Protocol
  - History
image: assets/img/blog/blog-4-scaled-2.png
### if need replace preview image for single image add field full_image, ex:
#full_image: assets/img/works/work4.jpg
author: Martin Kang(SY Kang)
tags: Protocol RFC6520 Heartbeat OpenSSL
---

## 1. HEARTBEAT 메시지의 도입 배경
&nbsp;Heartbeat 메시지는 SSL(Secure Sockets Layer) 위에서 동작하는 확장 기능으로 연결 상태를 주기적으로 확인하기 위해 도입되었다.  
<br>&nbsp;TLS는 TCP 기반 암호화 프로토콜로, 지속적인 데이터 전송 없이 연결 상태(Connection Alive)를 유지할 수 있는 기능이 반드시 제공되지 않는다. DTLS는 UDP 기반 암호화 프로토콜로, 데이터그램(Datagram)을 안전하게 전송하기 위해 설계되었다. 그러나 TLS와 달리 세션 관리 기능이 없어, 상대방이 통신 가능한 상태인지 확인하는 유일한 방법은 재협상(renegotiation) 뿐이다. 이러한 재협상은 Handshake 과정을 다시 수행해야 하므로, 특히 단방향 트래픽을 사용하는 애플리케이션에서는 비효율적이다.  
<br>&nbsp;이러한 한계를 해결하기 위해 2010년 6월 18일 [IETF의 초안 문서](https://datatracker.ietf.org/doc/html/draft-seggelmann-tls-dtls-heartbeat-00)에서 Heartbeat 메시지 개념이 제안되었다.
이후 해당 개념은 [RFC 6520](https://datatracker.ietf.org/doc/html/rfc6520) (Transport Layer Security (TLS) and Datagram Transport Layer Security (DTLS) Heartbeat Extension)으로 발전했으며, OpenSSL 측에서는 2012년 03월 14일에 공개된 [OpenSSL 1.0.1](https://github.com/openssl/openssl/releases/tag/OpenSSL_1_0_1) 버전에서 처음으로 도입되었다.
<br>
<br>
<br>

## 2. HEARTBEAT PROTOCOL 주요 요구사항
<img src="/assets/img/blog/heartbeat-diagram0.png" alt="Heartbeat Diagram" style="max-width: 100%; border-radius: 12px;" />  
<br>
＃ Heartbeat Protocol은 HeartbeatRequest, HeartbeatResponse 두 가지 메시지 타입으로 구성된다.  
＃ HeartbeatRequest 메시지는 연결이 유지되고 있는 동안 언제든지 도착할 수 있다.  
＃ HeartbeatRequest 메시지를 수신하면, 상응하는 HeartbeatResponse 메시지로 응답해야 한다.  
＃ HeartbeatRequest 수신 측은 동일한 페이로드를 포함하는 HeartbeatResponse 메시지를 반드시 전송해야 한다.  
＃ 수신된 Heartbeat 메시지의 페이로드 길이 필드 값이 너무 큰 경우 조용히 폐기되어야 한다.  
＃ 수신한 HeartbeatResponse 메시지가 HeartbeatRequest의 페이로드를 포함하지 않은 경우 조용히 폐기되어야 한다.  
＃ Heartbeat Protocol 메시지는 Heartbeat 메시지 타입 필드, unsigned int16(부호 없는 16bit 정수)를 범위로 하는 페이로드 길이 필드, 페이로드 필드, 패딩 필드로 구성된다.  
<br>
<br>
<br>

## 3. 정상적인 Heartbeat Message 동작 과정
<img src="/assets/img/blog/heartbeat-diagram1.png" alt="Heartbeat Diagram" style="max-width: 100%; border-radius: 12px;" />  
<br>
① 클라이언트와 서버가 암호화 통신을 위해 SSL/TLS Handshake를 한다.  
② 일정 주기로 클라이언트에서 서버로 HeartbeatRequest 메시지를 송신한다.  
③ 서버에서 HeartbeatRequest 메시지를 수신 후 버퍼에 저장한다.  
④ 버퍼에 저장한 HeartbeatRequest 정보를 바탕으로 HeartbeatResponse 메시지를 구성하여 클라이언트에 송신한다.  
⑤ 클라이언트에서 HeartbeatResponse 메시지를 수신하여 연결 상태를 확인한다.  
⑥ 이 과정을 반복한다.  

<script src="https://gist.github.com/SeungYoon7373/b28d97fde380a49822c8bdf024b49719.js"></script>

<!-- 
You'll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in [many different ways](https://jekyllrb.com/docs/usage/), but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts`[^posts] directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

[^posts]: Footnote test.

Jekyll also offers powerful support for code snippets:

```ruby
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
```

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll's GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].
 -->


[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
