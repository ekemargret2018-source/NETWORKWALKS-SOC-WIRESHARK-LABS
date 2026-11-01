# NETWORKWALKS-SOC-WIRESHARK-LABS
Wireshark SOC analyst labs 3 to 8 with screenshots and findings.

Hands-on Wireshark labs completed as a SOC analyst trainee. Each lab uses a captured network scenario, and I used filters, Statistics and Follow TCP Stream to find the evidence.

**Author:** Eke Margret Uloma

## Skills practised
- Capturing and filtering traffic in Wireshark
- Using Statistics (Conversations, Endpoints, Protocol Hierarchy)
- Following TCP streams to read plain text traffic
- Analysing SIP/RTP calls with Telephony, VoIP Calls
- Recognising attacks from packet patterns

## Lab 3: Port scan detection
- **Scenario:** unusual traffic to the company web server.
- **Finding:** one host sent 40 SYN packets in milliseconds, from one source port, to 40 different ports.
- **Evidence:** the server replied SYN, ACK on 3 open ports (22, 80, 443) and RST, ACK on 37 closed ports.
- **Result:** TCP SYN port scan (reconnaissance).
- **Useful filters:** `tcp.flags.syn == 1 && tcp.flags.ack == 0`, `tcp.flags.reset == 1`

## Lab 4: Plain text chat
- **Scenario:** an old office chat tool sending messages unencrypted.
- **Finding:** Follow TCP Stream on the chat port (5000) showed the full conversation, including a temporary password and a Wi-Fi password.
- **Risk:** anyone on the network could read credentials.
- **Useful filter:** `tcp.port == 5000`

## Lab 5: Hidden secret in a web page
- **Scenario:** an admin suspected a secret was left inside an intranet page.
- **Finding:** an HTML comment in `staff.html` held an internal server address and an access code. Browsers hide comments, but they are visible in the packets.
- **Useful filters:** `http.request`, `http contains "<!--"`

## Lab 6: Unencrypted web sign-in
- **Scenario:** an old staff portal running on plain HTTP.
- **Finding:** two POST requests to `/login` exposed the username and password in the HTML Form URL Encoded section.
- **Fix:** use HTTPS.
- **Useful filter:** `http.request.method == "POST"`

## Lab 7: FTP brute force
- **Scenario:** many failed FTP sign-ins.
- **Finding:** one host tried the username `admin` with many passwords, got 24 `530 Login incorrect` replies, then one `230 Login successful`.
- **Result:** brute-force attack that succeeded.
- **Useful filters:** `ftp.response.code == 530`, `ftp.response.code == 230`

## Lab 8: VoIP call playback
- **Scenario:** a phone call made through the company VoIP system.
- **Finding:** SIP set up the call and RTP (codec G.711 PCMU) carried the voice. I played it back with Telephony, VoIP Calls, Play Streams, and heard a payment card read out over the phone (details not shown here).
- **Fix:** encrypt calls with SRTP and SIPS/TLS.
- **Useful filters:** `sip`, `rtp`

## Main lesson
Unencrypted protocols (plain chat, HTTP, FTP and VoIP) can be read by anyone who captures the traffic. Encryption such as HTTPS, SFTP and SRTP protects the data, and limits on failed logins help stop brute-force attacks.

## Screenshots
See the `screenshots/` folder for evidence from each lab.
