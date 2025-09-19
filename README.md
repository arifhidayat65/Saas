# SaaS Zero Traffic Network Monitor

**A 100% functional home WiFi network monitoring system**

SaaS Zero Traffic Network Monitor is a comprehensive solution for monitoring and securing your home network. It provides real-time insights into network connections, bandwidth usage, security alerts, and process activities across all devices in your home network.

## Features

- **Real-time Network Monitoring**: Track all network connections in real-time
- **Process Analytics**: Monitor which processes are consuming bandwidth
- **Security Alerts**: Get notified of suspicious activities and connections
- **Multi-Device Support**: Monitor all devices in your home network (Windows, Linux, Android, etc.)
- **Bandwidth Analysis**: Understand which applications and devices are consuming your bandwidth
- **Connection Tracking**: Monitor established, listening, and suspicious connections
- **Dashboard Visualization**: Beautiful dashboard with charts and real-time data

## Architecture Overview

The system consists of three main components:

1. **Web Dashboard** (React + TypeScript + Vite)
2. **Network Agents** (Windows PowerShell & Linux Bash scripts)
3. **Database Backend** (Supabase with PostgreSQL)

## Prerequisites

Before installation, ensure you have:
- A [Supabase](https://supabase.com) account
- Node.js (v16 or higher)
- npm or bun package manager
- Access to network devices where agents will be installed

## Installation

### 1. Clone the Repository

```bash
git clone https://github.com/odaysec/SaaS-ZeroTraffic.git
cd SaaS-ZeroTraffic
```

### 2. Install Dependencies

```bash
npm install
```

or with Bun:

```bash
bun install
```

### 3. Database Setup (Supabase)

1. Create a new project in your Supabase dashboard
2. Navigate to the SQL Editor in your Supabase dashboard
3. Copy and execute the database schema from `supabase/seed.sql`
4. The schema includes:
   - Tables for organizations, hosts, processes, connections, network stats, and alerts
   - Functions for generating sample data
   - Row Level Security (RLS) policies

### 4. Configure Environment Variables

Create a `.env` file in the root directory with your Supabase credentials:

```env
VITE_SUPABASE_URL=your_supabase_project_url
VITE_SUPABASE_ANON_KEY=your_supabase_anon_key
```

### 5. Run the Dashboard

For development:

```bash
npm run dev
```

For production build:

```bash
npm run build
```

## Agent Installation

### Windows Agent

1. Download the agent script:
   ```powershell
   Invoke-WebRequest -Uri "https://raw.githubusercontent.com/odaysec/agent/windows-agent.ps1" -OutFile "windows-agent.ps1"
   ```

2. Set execution policy (if needed):
   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
   ```

3. Run the agent with your token:
   ```powershell
   .\windows-agent.ps1 -AgentToken "your-generated-token"
   ```

### Linux Agent

1. Download and make executable:
   ```bash
   curl -o linux-agent.sh https://raw.githubusercontent.com/odaysec/agent/linux-agent.sh
   chmod +x linux-agent.sh
   ```

2. Install dependencies:
   ```bash
   # Ubuntu/Debian
   sudo apt update
   sudo apt install curl jq net-tools
   
   # CentOS/RHEL
   sudo yum install curl jq net-tools
   ```

3. Run the agent with your token:
   ```bash
   ./linux-agent.sh -t "your-generated-token"
   ```

### Generate Agent Token

After setting up your admin account, generate an agent token using the Supabase SQL Editor:

```sql
INSERT INTO agent_tokens (organization_id, token, expires_at)
SELECT 
    o.id,
    'agent_' || encode(gen_random_bytes(32), 'hex'),
    now() + interval '1 year'
FROM organizations o
WHERE o.owner_id = (SELECT id FROM auth.users WHERE email = 'your-email@example.com');
```

## Monitoring Capabilities

### Process Information
- Chrome/Firefox connections and active tabs
- Steam/Games traffic and server connections
- Spotify/Netflix streaming bandwidth usage
- Windows Update system traffic
- Antivirus security scanning connections

### Network Connections
- Established connections (streaming, browsing, gaming)
- Listening services (web server, SSH, etc.)
- Time_Wait connections (recently closed)
- Foreign addresses (accessed servers/websites)

### Security Monitoring
- Unknown process detection
- Suspicious IP connections
- Port scanning attempts
- High bandwidth usage anomalies

## Dashboard Overview

The web dashboard provides:

1. **Statistics Cards**: Active hosts, total connections, bandwidth usage, security alerts
2. **Network Charts**: Real-time visualization of network traffic
3. **Alerts List**: Security and performance alerts requiring attention
4. **Process List**: Running processes consuming network resources
5. **Connections Table**: Detailed view of all network connections

## Security Best Practices

1. **Agent Security**: Agents only collect metadata, not payload data
2. **Token Management**: Store tokens securely and rotate them periodically
3. **Network Security**: All communications use HTTPS/TLS encryption
4. **Data Privacy**: No payload capture, local processing where possible, encrypted transit
5. **Retention Policy**: Automatic deletion of old data

## Development

### Available Scripts

- `npm run dev`: Start development server
- `npm run build`: Build for production
- `npm run build:dev`: Build for development
- `npm run lint`: Run ESLint
- `npm run preview`: Preview production build

### Project Structure

```
src/
├── components/    # React components
├── hooks/         # Custom React hooks
├── lib/           # Utility functions
├── pages/         # Page components
├── App.tsx        # Main application component
└── main.tsx       # Entry point
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Author

Created by [arifhidayat](https://github.com/adifhidayat65)

---

*Note: This is a monitoring tool designed for home network security and performance analysis. Always ensure you have proper authorization before monitoring any network.*
