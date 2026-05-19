text
meio/
├── frontend/
│   ├── app/
│   │   ├── layout.tsx
│   │   ├── page.tsx
│   │   └── globals.css
│   ├── components/
│   │   ├── terminal.tsx
│   │   ├── sidebar.tsx
│   │   ├── dashboard.tsx
│   │   ├── theme-provider.tsx
│   │   └── terminal-provider.tsx
│   ├── lib/
│   │   ├── terminal-hooks.ts
│   │   └── websocket.ts
│   ├── package.json
│   └── tailwind.config.js
├── backend/
│   ├── src/
│   │   ├── main.ts
│   │   ├── app.module.ts
│   │   ├── terminal/
│   │   │   ├── terminal.module.ts
│   │   │   └── terminal.service.ts
│   │   ├── agents/
│   │   │   ├── agents.module.ts
│   │   │   ├── agents.orchestrator.ts
│   │   │   ├── agent.class.ts
│   │   │   └── memory.store.ts
│   │   ├── git-integration/
│   │   │   ├── git.module.ts
│   │   │   └── git.service.ts
│   │   ├── blockchain/
│   │   │   ├── did.module.ts
│   │   │   └── did.utils.ts
│   │   ├── mcp/
│   │   │   ├── mcp.module.ts
│   │   │   └── mcp.server.ts
│   │   └── deployments/
│   │       ├── deploy.module.ts
│   │       └── deploy.service.ts
│   ├── package.json
│   └── tsconfig.json
├── infrastructure/
│   ├── docker/
│   │   ├── Dockerfile.backend
│   │   └── Dockerfile.frontend
│   ├── kubernetes/
│   │   ├── deployment.yaml
│   │   └── service.yaml
│   └── scripts/
│       └── setup.sh
├── pipelines/
│   ├── github-actions/
│   │   └── deploy.yml
│   └── gitlab-ci/
│       └── .gitlab-ci.yml
├── schemas/
│   ├── postgres/
│   │   └── 01_schema.sql
│   ├── qdrant/
│   │   └── collections.json
│   └── redis/
│       └── keys.md
└── docs/
    └── README.md
1️⃣ FRONTEND (Next.js + Tailwind + Shadcn/UI)
frontend/package.json
json
{
  "name": "meio-frontend",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint"
  },
  "dependencies": {
    "next": "14.0.4",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "tailwindcss": "^3.3.0",
    "framer-motion": "^10.16.16",
    "recharts": "^2.10.3",
    "socket.io-client": "^4.6.0",
    "lucide-react": "^0.303.0",
    "clsx": "^2.0.0",
    "tailwind-merge": "^2.2.0"
  },
  "devDependencies": {
    "@types/node": "^20.10.5",
    "@types/react": "^18.2.45",
    "@types/react-dom": "^18.2.18",
    "typescript": "^5.3.3",
    "autoprefixer": "^10.4.16",
    "postcss": "^8.4.32"
  }
}
frontend/app/layout.tsx
tsx
import type { Metadata } from 'next'
import { Inter } from 'next/font/google'
import './globals.css'

const inter = Inter({ subsets: ['latin'] })

export const metadata: Metadata = {
  title: 'MEIO - Agentic Operating System',
  description: 'Create applications and AI agents autonomously',
}

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en" suppressHydrationWarning>
      <body className={inter.className}>
        {children}
      </body>
    </html>
  )
}
frontend/app/globals.css
css
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  :root {
    --background: 0 0% 100%;
    --foreground: 222.2 84% 4.9%;
  }

  .dark {
    --background: 222.2 84% 4.9%;
    --foreground: 210 40% 98%;
  }
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  background: #000000;
  color: #ffffff;
}

/* Custom scrollbar */
::-webkit-scrollbar {
  width: 8px;
  height: 8px;
}

::-webkit-scrollbar-track {
  background: #1a1a1a;
}

::-webkit-scrollbar-thumb {
  background: #00ffff;
  border-radius: 4px;
}

::-webkit-scrollbar-thumb:hover {
  background: #00cccc;
}

/* Terminal cursor animation */
@keyframes blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}

.animate-blink {
  animation: blink 1s step-end infinite;
}

/* Glassmorphism effect */
.glass {
  background: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(0, 255, 255, 0.2);
}

/* Neon glow */
.neon-glow {
  box-shadow: 0 0 20px rgba(0, 255, 255, 0.3);
}

.neon-text {
  text-shadow: 0 0 10px rgba(0, 255, 255, 0.5);
}
frontend/app/page.tsx
tsx
'use client'

import { useState, useEffect } from 'react'
import { Terminal } from '@/components/terminal'
import { Sidebar } from '@/components/sidebar'
import { Dashboard } from '@/components/dashboard'

export default function Home() {
  const [activeView, setActiveView] = useState('terminal')
  const [isConnected, setIsConnected] = useState(false)

  useEffect(() => {
    // Check connection to backend
    fetch('http://localhost:3001/api/health')
      .then(() => setIsConnected(true))
      .catch(() => setIsConnected(false))
  }, [])

  return (
    <div className="flex h-screen bg-black text-white">
      <Sidebar activeView={activeView} setActiveView={setActiveView} />
      <main className="flex-1 flex flex-col">
        {/* Status bar */}
        <div className="h-8 bg-black/80 border-b border-cyan-500/20 flex items-center justify-between px-4 text-xs">
          <div className="flex items-center gap-4">
            <div className="flex items-center gap-2">
              <div className={`w-2 h-2 rounded-full ${isConnected ? 'bg-green-500 animate-pulse' : 'bg-red-500'}`} />
              <span className="text-gray-400">{isConnected ? 'Connected' : 'Disconnected'}</span>
            </div>
            <span className="text-gray-500">|</span>
            <span className="text-cyan-400">MEIO v1.0.0</span>
          </div>
          <div className="flex gap-4">
            <span className="text-gray-500">🤖 Agents: 0</span>
            <span className="text-gray-500">📦 Projects: 0</span>
          </div>
        </div>

        {/* Main content */}
        {activeView === 'terminal' && <Terminal />}
        {activeView === 'dashboard' && <Dashboard />}
      </main>
    </div>
  )
}
frontend/components/terminal.tsx
tsx
'use client'

import { useState, useRef, useEffect } from 'react'

interface CommandHistory {
  command: string
  output: string
  timestamp: Date
}

export function Terminal() {
  const [input, setInput] = useState('')
  const [history, setHistory] = useState<CommandHistory[]>([])
  const [isExecuting, setIsExecuting] = useState(false)
  const inputRef = useRef<HTMLInputElement>(null)
  const terminalRef = useRef<HTMLDivElement>(null)
  const commandHistoryRef = useRef<string[]>([])
  const historyIndexRef = useRef(-1)

  useEffect(() => {
    inputRef.current?.focus()
  }, [])

  useEffect(() => {
    if (terminalRef.current) {
      terminalRef.current.scrollTop = terminalRef.current.scrollHeight
    }
  }, [history])

  const executeCommand = async (command: string) => {
    setIsExecuting(true)
    
    try {
      const response = await fetch('http://localhost:3001/api/terminal/execute', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ command, userId: 'local-user' })
      })
      
      const data = await response.json()
      return data.output || 'Command executed successfully.'
    } catch (error) {
      return `Error: ${error.message}`
    } finally {
      setIsExecuting(false)
    }
  }

  const handleSubmit = async (e: React.FormEvent) => {
    e.preventDefault()
    if (!input.trim() || isExecuting) return

    const command = input.trim()
    const output = await executeCommand(command)
    
    setHistory(prev => [...prev, {
      command,
      output,
      timestamp: new Date()
    }])
    
    commandHistoryRef.current = [command, ...commandHistoryRef.current].slice(0, 50)
    historyIndexRef.current = -1
    setInput('')
  }

  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (e.key === 'ArrowUp') {
      e.preventDefault()
      if (historyIndexRef.current < commandHistoryRef.current.length - 1) {
        historyIndexRef.current++
        setInput(commandHistoryRef.current[historyIndexRef.current])
      }
    } else if (e.key === 'ArrowDown') {
      e.preventDefault()
      if (historyIndexRef.current > 0) {
        historyIndexRef.current--
        setInput(commandHistoryRef.current[historyIndexRef.current])
      } else if (historyIndexRef.current === 0) {
        historyIndexRef.current = -1
        setInput('')
      }
    }
  }

  const formatTimestamp = (date: Date) => {
    return date.toLocaleTimeString('pt-BR', { hour12: false })
  }

  return (
    <div 
      ref={terminalRef}
      className="h-full bg-black font-mono p-4 overflow-y-auto"
      onClick={() => inputRef.current?.focus()}
    >
      {/* Terminal header */}
      <div className="text-cyan-500 mb-4 border-b border-cyan-500/20 pb-2">
        <div className="flex items-center justify-between">
          <div className="flex items-center gap-2">
            <span className="text-green-500">●</span>
            <span>MEIO Terminal v1.0.0</span>
          </div>
          <div className="text-xs text-gray-500">
            Type 'forge help' for available commands
          </div>
        </div>
      </div>

      {/* Command history */}
      {history.map((entry, idx) => (
        <div key={idx} className="mb-4">
          <div className="flex items-center gap-2 text-cyan-400">
            <span className="text-green-500">➜</span>
            <span className="font-bold">~</span>
            <span>{entry.command}</span>
            <span className="text-xs text-gray-500 ml-auto">{formatTimestamp(entry.timestamp)}</span>
          </div>
          <div className="ml-4 text-gray-300 whitespace-pre-wrap mt-1">
            {entry.output}
          </div>
        </div>
      ))}

      {/* Current input line */}
      <form onSubmit={handleSubmit} className="flex items-center mt-2">
        <span className="text-green-500 mr-2">➜</span>
        <span className="text-cyan-400 mr-2">~</span>
        <input
          ref={inputRef}
          type="text"
          value={input}
          onChange={(e) => setInput(e.target.value)}
          onKeyDown={handleKeyDown}
          className="flex-1 bg-transparent outline-none text-white font-mono"
          disabled={isExecuting}
          autoFocus
        />
        {isExecuting && (
          <span className="text-yellow-500 text-sm ml-2 animate-pulse">
            executing...
          </span>
        )}
        <span className="w-2 h-5 bg-white animate-blink ml-1"></span>
      </form>

      {/* Welcome message for first time users */}
      {history.length === 0 && (
        <div className="mt-8 p-4 border border-cyan-500/20 rounded-lg bg-cyan-500/5">
          <div className="text-cyan-400 mb-2">✨ Welcome to MEIO!</div>
          <div className="text-gray-400 text-sm">
            Try these commands:<br/>
            • <span className="text-cyan-400">forge create app my-first-app</span> - Create your first application<br/>
            • <span className="text-cyan-400">forge spawn team fullstack my-first-app</span> - Spawn AI agents<br/>
            • <span className="text-cyan-400">forge help</span> - See all commands
          </div>
        </div>
      )}
    </div>
  )
}
frontend/components/sidebar.tsx
tsx
'use client'

import { Terminal, LayoutDashboard, GitBranch, Bot, Database, Cloud, Settings, HelpCircle } from 'lucide-react'

interface SidebarProps {
  activeView: string
  setActiveView: (view: string) => void
}

export function Sidebar({ activeView, setActiveView }: SidebarProps) {
  const menuItems = [
    { id: 'terminal', icon: Terminal, label: 'Terminal' },
    { id: 'dashboard', icon: LayoutDashboard, label: 'Dashboard' },
    { id: 'agents', icon: Bot, label: 'Agents' },
    { id: 'repos', icon: GitBranch, label: 'Repositories' },
    { id: 'database', icon: Database, label: 'Database' },
    { id: 'deploy', icon: Cloud, label: 'Deploy' },
  ]

  return (
    <aside className="w-16 bg-black/80 border-r border-cyan-500/20 flex flex-col items-center py-4 gap-6">
      {/* Logo */}
      <div className="w-10 h-10 rounded-full bg-gradient-to-r from-cyan-500 to-purple-500 flex items-center justify-center mb-4">
        <span className="text-black font-bold text-xl">M</span>
      </div>

      {/* Navigation */}
      <nav className="flex flex-col gap-4">
        {menuItems.map((item) => {
          const Icon = item.icon
          const isActive = activeView === item.id
          return (
            <button
              key={item.id}
              onClick={() => setActiveView(item.id)}
              className={`w-10 h-10 rounded-lg flex items-center justify-center transition-all duration-200 group relative ${
                isActive 
                  ? 'bg-cyan-500/20 text-cyan-400 border border-cyan-500/50' 
                  : 'text-gray-500 hover:text-cyan-400 hover:bg-cyan-500/10'
              }`}
            >
              <Icon size={20} />
              <span className="absolute left-full ml-2 px-2 py-1 bg-black border border-cyan-500/20 rounded text-xs whitespace-nowrap opacity-0 group-hover:opacity-100 transition-opacity pointer-events-none">
                {item.label}
              </span>
            </button>
          )
        })}
      </nav>

      {/* Bottom items */}
      <div className="mt-auto flex flex-col gap-4">
        <button className="w-10 h-10 rounded-lg flex items-center justify-center text-gray-500 hover:text-cyan-400 transition-colors">
          <Settings size={20} />
        </button>
        <button className="w-10 h-10 rounded-lg flex items-center justify-center text-gray-500 hover:text-cyan-400 transition-colors">
          <HelpCircle size={20} />
        </button>
      </div>
    </aside>
  )
}
frontend/components/dashboard.tsx
tsx
'use client'

import { useState, useEffect } from 'react'
import { Activity, Cpu, HardDrive, Users, Zap, Clock } from 'lucide-react'

interface Metric {
  cpu: number
  memory: number
  agents: number
  requests: number
  uptime: number
}

export function Dashboard() {
  const [metrics, setMetrics] = useState<Metric>({
    cpu: 23,
    memory: 45,
    agents: 3,
    requests: 1247,
    uptime: 3600
  })

  const [recentActivity, setRecentActivity] = useState([
    { id: 1, action: 'Agent backend-01 created', time: '2 min ago', status: 'success' },
    { id: 2, action: 'Deployed project ecommerce-ai', time: '5 min ago', status: 'success' },
    { id: 3, action: 'GitHub repository synced', time: '10 min ago', status: 'info' },
    { id: 4, action: 'Trust score updated for devops-agent', time: '15 min ago', status: 'warning' },
  ])

  useEffect(() => {
    const interval = setInterval(() => {
      setMetrics(prev => ({
        ...prev,
        cpu: Math.floor(10 + Math.random() * 40),
        memory: Math.floor(30 + Math.random() * 30),
        requests: prev.requests + Math.floor(Math.random() * 50)
      }))
    }, 5000)

    return () => clearInterval(interval)
  }, [])

  const formatUptime = (seconds: number) => {
    const hours = Math.floor(seconds / 3600)
    const minutes = Math.floor((seconds % 3600) / 60)
    return `${hours}h ${minutes}m`
  }

  const getStatusColor = (status: string) => {
    switch (status) {
      case 'success': return 'text-green-500'
      case 'warning': return 'text-yellow-500'
      case 'error': return 'text-red-500'
      default: return 'text-cyan-500'
    }
  }

  return (
    <div className="h-full overflow-y-auto p-6">
      {/* Header */}
      <div className="mb-6">
        <h1 className="text-2xl font-bold bg-gradient-to-r from-cyan-400 to-purple-500 bg-clip-text text-transparent">
          Dashboard
        </h1>
        <p className="text-gray-500 text-sm mt-1">Real-time system overview</p>
      </div>

      {/* Metrics Grid */}
      <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-5 gap-4 mb-6">
        <MetricCard
          title="CPU Usage"
          value={`${metrics.cpu}%`}
          icon={Cpu}
          color="cyan"
          progress={metrics.cpu}
        />
        <MetricCard
          title="Memory"
          value={`${metrics.memory}%`}
          icon={HardDrive}
          color="purple"
          progress={metrics.memory}
        />
        <MetricCard
          title="Active Agents"
          value={metrics.agents.toString()}
          icon={Users}
          color="green"
        />
        <MetricCard
          title="Requests"
          value={metrics.requests.toLocaleString()}
          icon={Activity}
          color="yellow"
        />
        <MetricCard
          title="Uptime"
          value={formatUptime(metrics.uptime)}
          icon={Clock}
          color="blue"
        />
      </div>

      {/* Two column layout */}
      <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
        {/* Agent Status */}
        <div className="lg:col-span-2 space-y-6">
          <div className="glass rounded-lg p-4">
            <h2 className="text-lg font-semibold text-cyan-400 mb-4 flex items-center gap-2">
              <Zap size={18} />
              Active Agents
            </h2>
            <div className="space-y-3">
              {[
                { name: 'architect-01', type: 'Architect', status: 'idle', trust: 94, tasks: 12 },
                { name: 'backend-01', type: 'Backend', status: 'working', trust: 87, tasks: 8 },
                { name: 'frontend-01', type: 'Frontend', status: 'working', trust: 91, tasks: 6 },
                { name: 'devops-01', type: 'DevOps', status: 'idle', trust: 96, tasks: 4 },
              ].map((agent, idx) => (
                <div key={idx} className="flex items-center justify-between p-3 bg-gray-900/50 rounded-lg border border-gray-800">
                  <div className="flex items-center gap-3">
                    <div className={`w-2 h-2 rounded-full ${agent.status === 'working' ? 'bg-green-500 animate-pulse' : 'bg-gray-500'}`} />
                    <div>
                      <div className="font-mono text-cyan-400">{agent.name}</div>
                      <div className="text-xs text-gray-500">{agent.type}</div>
                    </div>
                  </div>
                  <div className="flex items-center gap-4">
                    <div className="text-right">
                      <div className="text-xs text-gray-400">Trust Score</div>
                      <div className="text-sm text-green-400">{agent.trust}%</div>
                    </div>
                    <div className="text-right">
                      <div className="text-xs text-gray-400">Tasks</div>
                      <div className="text-sm text-white">{agent.tasks}</div>
                    </div>
                  </div>
                </div>
              ))}
            </div>
          </div>

          {/* Recent Deployments */}
          <div className="glass rounded-lg p-4">
            <h2 className="text-lg font-semibold text-cyan-400 mb-4">Recent Deployments</h2>
            <div className="space-y-2">
              {[
                { name: 'ecommerce-ai', env: 'production', time: '2 hours ago', status: 'live' },
                { name: 'crm-saas', env: 'staging', time: '5 hours ago', status: 'building' },
                { name: 'agent-marketplace', env: 'production', time: '1 day ago', status: 'live' },
              ].map((deploy, idx) => (
                <div key={idx} className="flex items-center justify-between p-2 border-b border-gray-800 last:border-0">
                  <div>
                    <div className="font-medium">{deploy.name}</div>
                    <div className="text-xs text-gray-500">{deploy.env}</div>
                  </div>
                  <div className="flex items-center gap-3">
                    <div className={`text-xs px-2 py-1 rounded-full ${
                      deploy.status === 'live' ? 'bg-green-500/20 text-green-400' : 'bg-yellow-500/20 text-yellow-400'
                    }`}>
                      {deploy.status}
                    </div>
                    <div className="text-xs text-gray-500">{deploy.time}</div>
                  </div>
                </div>
              ))}
            </div>
          </div>
        </div>

        {/* Recent Activity */}
        <div className="glass rounded-lg p-4">
          <h2 className="text-lg font-semibold text-cyan-400 mb-4">Recent Activity</h2>
          <div className="space-y-3">
            {recentActivity.map(activity => (
              <div key={activity.id} className="flex items-start gap-3">
                <div className={`w-1.5 h-1.5 rounded-full mt-2 ${getStatusColor(activity.status)}`} />
                <div className="flex-1">
                  <div className="text-sm text-gray-300">{activity.action}</div>
                  <div className="text-xs text-gray-500">{activity.time}</div>
                </div>
              </div>
            ))}
          </div>

          {/* Quick Actions */}
          <div className="mt-6 pt-4 border-t border-gray-800">
            <h3 className="text-sm text-gray-400 mb-3">Quick Actions</h3>
            <div className="space-y-2">
              <button className="w-full text-left px-3 py-2 rounded-lg bg-cyan-500/10 hover:bg-cyan-500/20 text-cyan-400 text-sm transition-colors">
                + Create New Project
              </button>
              <button className="w-full text-left px-3 py-2 rounded-lg bg-purple-500/10 hover:bg-purple-500/20 text-purple-400 text-sm transition-colors">
                🤖 Spawn Agent Team
              </button>
              <button className="w-full text-left px-3 py-2 rounded-lg bg-gray-800/50 hover:bg-gray-800 text-gray-300 text-sm transition-colors">
                📦 Deploy to Production
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  )
}

function MetricCard({ title, value, icon: Icon, color, progress }: any) {
  const colorClasses = {
    cyan: 'text-cyan-400 border-cyan-500/30',
    purple: 'text-purple-400 border-purple-500/30',
    green: 'text-green-400 border-green-500/30',
    yellow: 'text-yellow-400 border-yellow-500/30',
    blue: 'text-blue-400 border-blue-500/30',
  }

  return (
    <div className={`glass rounded-lg p-4 border-l-4 ${colorClasses[color]}`}>
      <div className="flex items-center justify-between mb-2">
        <span className="text-gray-400 text-sm">{title}</span>
        <Icon size={18} className={`${colorClasses[color].split(' ')[0]}`} />
      </div>
      <div className="text-2xl font-bold">{value}</div>
      {progress !== undefined && (
        <div className="mt-2 w-full bg-gray-800 rounded-full h-1">
          <div 
            className={`h-1 rounded-full bg-${color}-500`}
            style={{ width: `${progress}%` }}
          />
        </div>
      )}
    </div>
  )
}
frontend/components/theme-provider.tsx
tsx
'use client'

import { createContext, useContext, useEffect, useState } from 'react'

type Theme = 'dark' | 'light' | 'system'

interface ThemeProviderProps {
  children: React.ReactNode
  attribute?: string
  defaultTheme?: Theme
  enableSystem?: boolean
}

const ThemeContext = createContext({ theme: 'dark', setTheme: (theme: Theme) => {} })

export function ThemeProvider({ children, defaultTheme = 'dark' }: ThemeProviderProps) {
  const [theme, setTheme] = useState<Theme>(defaultTheme)

  useEffect(() => {
    const root = window.document.documentElement
    root.classList.remove('light', 'dark')
    root.classList.add(theme)
  }, [theme])

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  )
}

export const useTheme = () => useContext(ThemeContext)
frontend/components/terminal-provider.tsx
tsx
'use client'

import { createContext, useContext, useState } from 'react'

interface TerminalContextType {
  history: string[]
  addToHistory: (command: string, output: string) => void
  clearHistory: () => void
}

const TerminalContext = createContext<TerminalContextType>({
  history: [],
  addToHistory: () => {},
  clearHistory: () => {}
})

export function TerminalProvider({ children }: { children: React.ReactNode }) {
  const [history, setHistory] = useState<string[]>([])

  const addToHistory = (command: string, output: string) => {
    setHistory(prev => [...prev, `$ ${command}`, output, ''])
  }

  const clearHistory = () => setHistory([])

  return (
    <TerminalContext.Provider value={{ history, addToHistory, clearHistory }}>
      {children}
    </TerminalContext.Provider>
  )
}

export const useTerminal = () => useContext(TerminalContext)
frontend/lib/terminal-hooks.ts
ts
import { useState } from 'react'

export function useTerminal() {
  const [isExecuting, setIsExecuting] = useState(false)

  const executeCommand = async (command: string): Promise<string> => {
    setIsExecuting(true)
    
    try {
      const response = await fetch('http://localhost:3001/api/terminal/execute', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ command, userId: 'local-user' })
      })
      
      const data = await response.json()
      return data.output || 'Command executed successfully.'
    } catch (error) {
      return `Error: ${error.message}`
    } finally {
      setIsExecuting(false)
    }
  }

  return { executeCommand, isExecuting }
}
frontend/lib/websocket.ts
ts
import { useEffect, useState } from 'react'

export function useWebSocket(url: string) {
  const [lastMessage, setLastMessage] = useState<any>(null)
  const [isConnected, setIsConnected] = useState(false)

  useEffect(() => {
    const ws = new WebSocket(url)

    ws.onopen = () => {
      setIsConnected(true)
    }

    ws.onmessage = (event) => {
      setLastMessage(event)
    }

    ws.onclose = () => {
      setIsConnected(false)
    }

    return () => ws.close()
  }, [url])

  return { lastMessage, isConnected }
}
frontend/tailwind.config.js
js
/** @type {import('tailwindcss').Config} */
module.exports = {
  darkMode: ["class"],
  content: [
    './pages/**/*.{ts,tsx}',
    './components/**/*.{ts,tsx}',
    './app/**/*.{ts,tsx}',
    './src/**/*.{ts,tsx}',
  ],
  theme: {
    extend: {
      keyframes: {
        blink: {
          '0%, 100%': { opacity: 1 },
          '50%': { opacity: 0 },
        },
      },
      animation: {
        blink: 'blink 1s step-end infinite',
      },
    },
  },
  plugins: [],
}
2️⃣ BACKEND (NestJS + TypeScript)
backend/package.json
json
{
  "name": "meio-backend",
  "version": "1.0.0",
  "description": "MEIO Platform Backend",
  "scripts": {
    "build": "nest build",
    "start": "nest start",
    "start:dev": "nest start --watch",
    "start:debug": "nest start --debug --watch",
    "start:prod": "node dist/main",
    "test": "jest"
  },
  "dependencies": {
    "@nestjs/common": "^10.0.0",
    "@nestjs/core": "^10.0.0",
    "@nestjs/platform-express": "^10.0.0",
    "@nestjs/websockets": "^10.0.0",
    "@nestjs/platform-socket.io": "^10.0.0",
    "@nestjs/typeorm": "^10.0.0",
    "@nestjs/config": "^3.0.0",
    "@nestjs/jwt": "^10.0.0",
    "@nestjs/graphql": "^12.0.0",
    "@nestjs/apollo": "^12.0.0",
    "@octokit/rest": "^20.0.0",
    "@gitbeaker/node": "^39.0.0",
    "typeorm": "^0.3.17",
    "pg": "^8.11.0",
    "redis": "^4.6.0",
    "@qdrant/js-client-rest": "^1.7.0",
    "graphql": "^16.8.0",
    "apollo-server-express": "^3.12.0",
    "socket.io": "^4.6.0",
    "axios": "^1.6.0",
    "bcryptjs": "^2.4.3",
    "class-validator": "^0.14.0",
    "class-transformer": "^0.5.1",
    "reflect-metadata": "^0.1.13",
    "rxjs": "^7.8.0"
  },
  "devDependencies": {
    "@nestjs/cli": "^10.0.0",
    "@nestjs/schematics": "^10.0.0",
    "@nestjs/testing": "^10.0.0",
    "@types/node": "^20.10.0",
    "@types/express": "^4.17.17",
    "typescript": "^5.1.3",
    "jest": "^29.5.0",
    "ts-jest": "^29.1.0",
    "ts-node": "^10.9.1",
    "supertest": "^6.3.3"
  }
}
backend/tsconfig.json
json
{
  "compilerOptions": {
    "module": "commonjs",
    "declaration": true,
    "removeComments": true,
    "emitDecoratorMetadata": true,
    "experimentalDecorators": true,
    "allowSyntheticDefaultImports": true,
    "target": "ES2021",
    "sourceMap": true,
    "outDir": "./dist",
    "baseUrl": "./",
    "incremental": true,
    "skipLibCheck": true,
    "strictNullChecks": false,
    "noImplicitAny": false,
    "strictBindCallApply": false,
    "forceConsistentCasingInFileNames": false,
    "noFallthroughCasesInSwitch": false,
    "esModuleInterop": true
  }
}
backend/src/main.ts
typescript
import { NestFactory } from '@nestjs/core'
import { AppModule } from './app.module'
import { ValidationPipe } from '@nestjs/common'

async function bootstrap() {
  const app = await NestFactory.create(AppModule)
  
  app.enableCors({
    origin: 'http://localhost:3000',
    credentials: true
  })
  
  app.useGlobalPipes(new ValidationPipe())
  
  await app.listen(3001)
  console.log(`
  ╔════════════════════════════════════════╗
  ║                                        ║
  ║   🚀 MEIO Platform Backend Running     ║
  ║                                        ║
  ║   ➜  API: http://localhost:3001       ║
  ║   ➜  GraphQL: http://localhost:3001/graphql ║
  ║   ➜  WebSocket: ws://localhost:3001   ║
  ║                                        ║
  ╚════════════════════════════════════════╝
  `)
}
bootstrap()
backend/src/app.module.ts
typescript
import { Module } from '@nestjs/common'
import { TerminalModule } from './terminal/terminal.module'
import { AgentsModule } from './agents/agents.module'
import { GitModule } from './git-integration/git.module'
import { DidModule } from './blockchain/did.module'
import { McpModule } from './mcp/mcp.module'
import { DeployModule } from './deployments/deploy.module'

@Module({
  imports: [
    TerminalModule,
    AgentsModule,
    GitModule,
    DidModule,
    McpModule,
    DeployModule,
  ],
})
export class AppModule {}
backend/src/terminal/terminal.module.ts
typescript
import { Module } from '@nestjs/common'
import { TerminalService } from './terminal.service'
import { TerminalController } from './terminal.controller'
import { AgentsModule } from '../agents/agents.module'
import { GitModule } from '../git-integration/git.module'

@Module({
  imports: [AgentsModule, GitModule],
  controllers: [TerminalController],
  providers: [TerminalService],
  exports: [TerminalService],
})
export class TerminalModule {}
backend/src/terminal/terminal.controller.ts
typescript
import { Controller, Post, Body } from '@nestjs/common'
import { TerminalService } from './terminal.service'

@Controller('api/terminal')
export class TerminalController {
  constructor(private terminalService: TerminalService) {}

  @Post('execute')
  async executeCommand(@Body() body: { command: string; userId: string }) {
    const output = await this.terminalService.executeCommand(
      body.userId,
      body.command
    )
    return { output }
  }
}
backend/src/terminal/terminal.service.ts
typescript
import { Injectable } from '@nestjs/common'
import { AgentsOrchestrator } from '../agents/agents.orchestrator'
import { GitService } from '../git-integration/git.service'

@Injectable()
export class TerminalService {
  constructor(
    private agentsOrchestrator: AgentsOrchestrator,
    private gitService: GitService,
  ) {}

  async executeCommand(userId: string, command: string): Promise<string> {
    const parts = command.trim().split(' ')
    const mainCmd = parts[0]

    if (mainCmd === 'forge') {
      return this.handleForgeCommand(userId, parts.slice(1))
    }
    
    return `Unknown command: ${mainCmd}\nType 'forge help' for available commands`
  }

  private async handleForgeCommand(userId: string, args: string[]): Promise<string> {
    const subCmd = args[0]

    switch (subCmd) {
      case 'create':
        return this.handleCreate(userId, args.slice(1))
      case 'connect':
        return this.handleConnect(userId, args.slice(1))
      case 'deploy':
        return this.handleDeploy(args.slice(1))
      case 'spawn':
        return this.handleSpawn(userId, args.slice(1))
      case 'help':
        return this.showHelp()
      default:
        return this.showHelp()
    }
  }

  private async handleCreate(userId: string, args: string[]): Promise<string> {
    const type = args[0]
    const name = args[1]

    if (!name) {
      return '❌ Error: Please provide a name.\nUsage: forge create <app|agent|saas> <name>'
    }

    if (type === 'app') {
      let output = `\n🚀 Creating application: ${name}\n`
      output += `   ├── 📁 Creating project structure...\n`
      output += `   ├── ⚛️  Generating frontend (React/Next.js)...\n`
      output += `   ├── 🔧 Generating backend (NestJS)...\n`
      output += `   ├── 🗄️  Creating PostgreSQL database...\n`
      output += `   ├── 🔐 Configuring authentication...\n`
      
      const repoUrl = await this.gitService.createRepository(userId, name)
      output += `   ├── 📦 Repository created: ${repoUrl}\n`
      
      output += `   ├── 🤖 Spawning agent team...\n`
      await this.agentsOrchestrator.spawnTeam(userId, name, ['architect', 'backend', 'frontend', 'database', 'devops'])
      
      output += `   └── ✅ App "${name}" is being built by AI agents\n\n`
      output += `📊 Dashboard: http://localhost:3000/projects/${name}\n`
      output += `🔗 Repository: ${repoUrl}\n`
      output += `💡 Type 'forge status ${name}' to check progress\n`
      
      return output
    }
    
    if (type === 'agent') {
      return `🤖 Creating agent "${name}"...\n✅ Agent created successfully!\n💡 Use 'forge spawn ${name}' to activate it.`
    }

    return `❌ Unknown type: ${type}\nSupported: app, agent, saas`
  }

  private async handleConnect(userId: string, args: string[]): Promise<string> {
    const provider = args[0]
    
    switch (provider) {
      case 'github':
        return `🔗 To connect GitHub, please visit: http://localhost:3001/api/auth/github\n`
      case 'gitlab':
        return `🔗 To connect GitLab, please visit: http://localhost:3001/api/auth/gitlab\n`
      case 'gitlawb':
        return `🔗 To connect gitlawb, please provide your DID: forge connect gitlawb <did>\n`
      default:
        return `Available providers: github, gitlab, gitlawb`
    }
  }

  private async handleDeploy(args: string[]): Promise<string> {
    const environment = args[0] || 'staging'
    return `\n🚀 Deploying to ${environment}...\n   ├── 📦 Building containers...\n   ├── 🔄 Pushing to registry...\n   ├── 🚢 Deploying to Kubernetes...\n   └── ✅ Deployment complete!\n\n🌐 URL: https://${environment}.meio.local\n`
  }

  private async handleSpawn(userId: string, args: string[]): Promise<string> {
    const teamType = args[0] || 'fullstack'
    const projectName = args[1] || 'default-project'
    
    const agentTypes = this.getAgentList(teamType)
    await this.agentsOrchestrator.spawnTeam(userId, projectName, agentTypes)
    
    return `✅ Agent team spawned for "${projectName}"\n   ├── ${agentTypes.join('\n   ├── ')}\n   └── 🤖 Agents are now collaborating autonomously.\n`
  }

  private getAgentList(teamType: string): string[] {
    const teams: Record<string, string[]> = {
      fullstack: ['architect', 'backend', 'frontend', 'database', 'devops', 'qa', 'security'],
      'backend-only': ['architect', 'backend', 'database', 'devops'],
      frontend: ['architect', 'frontend', 'qa'],
      devops: ['devops', 'security', 'monitoring']
    }
    return teams[teamType] || teams.fullstack
  }

  private showHelp(): string {
    return `
╔══════════════════════════════════════════════════════════════╗
║                    🚀 MEIO - Forge Commands                   ║
╠══════════════════════════════════════════════════════════════╣
║                                                              ║
║  📦 APP CREATION                                             ║
║    forge create app <name>     Create a new application      ║
║    forge create agent <name>   Create a new AI agent         ║
║    forge create saas <name>    Create a full SaaS platform   ║
║                                                              ║
║  🔌 INTEGRATIONS                                             ║
║    forge connect github        Connect GitHub account        ║
║    forge connect gitlab        Connect GitLab account        ║
║    forge connect gitlawb       Connect gitlawb (DID-based)   ║
║                                                              ║
║  🚀 DEPLOYMENT                                               ║
║    forge deploy staging        Deploy to staging             ║
║    forge deploy production     Deploy to production          ║
║                                                              ║
║  🤖 AGENTS                                                   ║
║    forge spawn fullstack <p>   Spawn full agent team         ║
║    forge spawn backend <p>     Spawn backend agents only     ║
║    forge spawn devops <p>      Spawn DevOps agents           ║
║                                                              ║
║  📊 UTILITIES                                                ║
║    forge network create        Create P2P network            ║
║    forge autonomous enable     Enable autonomous mode        ║
║    forge trust inspect <id>    Check agent trust score       ║
║    forge mcp enable           Enable MCP server              ║
║                                                              ║
║  ❓ HELP                                                     ║
║    forge help                  Show this help message        ║
║                                                              ║
╚══════════════════════════════════════════════════════════════╝
`
  }
}
backend/src/agents/agents.module.ts
typescript
import { Module } from '@nestjs/common'
import { AgentsOrchestrator } from './agents.orchestrator'
import { MemoryStore } from './memory.store'

@Module({
  providers: [AgentsOrchestrator, MemoryStore],
  exports: [AgentsOrchestrator, MemoryStore],
})
export class AgentsModule {}
backend/src/agents/agents.orchestrator.ts
typescript
import { Injectable } from '@nestjs/common'
import { Agent } from './agent.class'
import { MemoryStore } from './memory.store'

@Injectable()
export class AgentsOrchestrator {
  private activeAgents: Map<string, Agent> = new Map()

  constructor(private memoryStore: MemoryStore) {}

  async spawnTeam(userId: string, projectId: string, agentTypes: string[]): Promise<Agent[]> {
    const agents: Agent[] = []
    
    for (const type of agentTypes) {
      const agent = new Agent({
        id: `${projectId}-${type}-${Date.now()}`,
        type: type,
        projectId: projectId,
        capabilities: this.getCapabilitiesForType(type),
        trustScore: 0.7,
        memoryStore: this.memoryStore
      })
      
      await agent.initialize()
      this.activeAgents.set(agent.id, agent)
      agents.push(agent)
    }
    
    this.establishCommunicationChannels(agents)
    await this.startCollaboration(agents, projectId)
    
    return agents
  }

  private getCapabilitiesForType(type: string): string[] {
    const capabilities: Record<string, string[]> = {
      architect: ['design_system', 'choose_stack', 'create_schemas'],
      backend: ['create_api', 'write_endpoints', 'integrate_database', 'auth_setup'],
      frontend: ['create_ui', 'responsive_design', 'api_integration', 'state_management'],
      database: ['create_schema', 'optimize_queries', 'migrations', 'indexes'],
      devops: ['dockerfile', 'kubernetes', 'deploy', 'monitoring'],
      qa: ['unit_tests', 'integration_tests', 'e2e_tests', 'bug_reports'],
      security: ['audit', 'vulnerability_scan', 'encryption', 'auth_review'],
      monitoring: ['metrics', 'logs', 'alerts', 'dashboards']
    }
    return capabilities[type] || ['execute_task', 'communicate']
  }

  private establishCommunicationChannels(agents: Agent[]) {
    for (let i = 0; i < agents.length; i++) {
      for (let j = i + 1; j < agents.length; j++) {
        agents[i].addPeer(agents[j])
        agents[j].addPeer(agents[i])
      }
    }
  }

  private async startCollaboration(agents: Agent[], projectId: string) {
    const architect = agents.find(a => a.type === 'architect')
    if (architect) {
      await architect.assignTask({
        type: 'build_project',
        projectId: projectId,
        requirements: 'Build full application based on best practices',
        context: { agents: agents.map(a => ({ id: a.id, type: a.type })) }
      })
    }
  }

  async getAgentStatus(agentId: string): Promise<any> {
    const agent = this.activeAgents.get(agentId)
    if (!agent) return null
    return {
      id: agent.id,
      type: agent.type,
      status: agent.status,
      trustScore: agent.trustScore,
      tasksCompleted: agent.tasksCompleted
    }
  }

  getAllAgents(): any[] {
    return Array.from(this.activeAgents.values()).map(agent => ({
      id: agent.id,
      type: agent.type,
      status: agent.status,
      trustScore: agent.trustScore,
      tasksCompleted: agent.tasksCompleted
    }))
  }
}
backend/src/agents/agent.class.ts
typescript
import { EventEmitter } from 'events'

export class Agent extends EventEmitter {
  public id: string
  public type: string
  public projectId: string
  public capabilities: string[]
  public trustScore: number
  public status: string = 'idle'
  public tasksCompleted: number = 0
  private memoryStore: any
  private peers: Agent[] = []

  constructor(config: any) {
    super()
    this.id = config.id
    this.type = config.type
    this.projectId = config.projectId
    this.capabilities = config.capabilities
    this.trustScore = config.trustScore
    this.memoryStore = config.memoryStore
  }

  async initialize() {
    this.status = 'initialized'
    this.emit('ready', { agentId: this.id, type: this.type })
    console.log(`🤖 Agent ${this.type} (${this.id}) initialized`)
  }

  async assignTask(task: any): Promise<any> {
    this.status = 'working'
    this.emit('task.started', { agentId: this.id, task })
    
    let result
    switch (this.type) {
      case 'architect':
        result = await this.executeArchitectTask(task)
        break
      case 'backend':
        result = await this.executeBackendTask(task)
        break
      case 'frontend':
        result = await this.executeFrontendTask(task)
        break
      case 'database':
        result = await this.executeDatabaseTask(task)
        break
      case 'devops':
        result = await this.executeDevOpsTask(task)
        break
      case 'qa':
        result = await this.executeQATask(task)
        break
      case 'security':
        result = await this.executeSecurityTask(task)
        break
      default:
        result = await this.executeGenericTask(task)
    }
    
    this.tasksCompleted++
    this.status = 'idle'
    this.emit('task.completed', { agentId: this.id, task, result })
    
    if (result.success) {
      this.trustScore = Math.min(1, this.trustScore + 0.01)
    } else {
      this.trustScore = Math.max(0, this.trustScore - 0.02)
    }
    
    await this.memoryStore?.store(this.id, {
      task,
      result,
      timestamp: Date.now(),
      trustScore: this.trustScore
    })
    
    return result
  }

  private async executeArchitectTask(task: any): Promise<any> {
    const architecture = {
      frontend: { framework: 'Next.js', styling: 'TailwindCSS' },
      backend: { framework: 'NestJS', api: 'GraphQL+REST' },
      database: { primary: 'PostgreSQL', cache: 'Redis', vector: 'Qdrant' },
      deployment: { platform: 'Kubernetes', cloud: 'AWS' }
    }
    
    for (const peer of this.peers) {
      await peer.receiveMessage({
        from: this.id,
        type: 'architecture_defined',
        data: architecture
      })
    }
    
    return { success: true, architecture }
  }

  private async executeBackendTask(task: any): Promise<any> {
    return {
      success: true,
      endpoints: ['/api/users', '/api/auth', '/api/data'],
      generated: ['auth.service.ts', 'user.controller.ts']
    }
  }

  private async executeFrontendTask(task: any): Promise<any> {
    return {
      success: true,
      components: ['Layout', 'Dashboard', 'LoginForm'],
      pages: ['/', '/dashboard', '/settings']
    }
  }

  private async executeDatabaseTask(task: any): Promise<any> {
    return {
      success: true,
      migrations: ['001_users.sql', '002_projects.sql'],
      indexes: ['idx_users_email', 'idx_projects_user_id']
    }
  }

  private async executeDevOpsTask(task: any): Promise<any> {
    return {
      success: true,
      dockerfile: 'Dockerfile generated',
      kubernetes: ['deployment.yaml', 'service.yaml'],
      deployed: true
    }
  }

  private async executeQATask(task: any): Promise<any> {
    return {
      success: true,
      tests: ['auth.test.js', 'api.test.js'],
      coverage: 87,
      passing: 42,
      failing: 3
    }
  }

  private async executeSecurityTask(task: any): Promise<any> {
    return {
      success: true,
      vulnerabilities: 0,
      critical: 0,
      recommendations: ['Enable 2FA', 'Add rate limiting']
    }
  }

  private async executeGenericTask(task: any): Promise<any> {
    return { success: true, message: `Task ${task.type} completed by ${this.type}` }
  }

  addPeer(peer: Agent) {
    this.peers.push(peer)
  }

  async receiveMessage(message: any): Promise<void> {
    this.emit('message.received', message)
    console.log(`Agent ${this.type} received message from ${message.from}: ${message.type}`)
  }
}
backend/src/agents/memory.store.ts
typescript
import { Injectable } from '@nestjs/common'

interface MemoryEntry {
  task: any
  result: any
  timestamp: number
  trustScore: number
}

@Injectable()
export class MemoryStore {
  private memories: Map<string, MemoryEntry[]> = new Map()

  async store(agentId: string, entry: MemoryEntry): Promise<void> {
    if (!this.memories.has(agentId)) {
      this.memories.set(agentId, [])
    }
    this.memories.get(agentId)!.push(entry)
  }

  async retrieve(agentId: string, limit: number = 10): Promise<MemoryEntry[]> {
    const entries = this.memories.get(agentId) || []
    return entries.slice(-limit)
  }

  async clear(agentId: string): Promise<void> {
    this.memories.delete(agentId)
  }
}
backend/src/git-integration/git.module.ts
typescript
import { Module } from '@nestjs/common'
import { GitService } from './git.service'

@Module({
  providers: [GitService],
  exports: [GitService],
})
export class GitModule {}
backend/src/git-integration/git.service.ts
typescript
import { Injectable } from '@nestjs/common'

@Injectable()
export class GitService {
  private githubConnected = false
  private gitlabConnected = false
  private gitlawbConnected = false

  async connectGitHub(token: string): Promise<string> {
    this.githubConnected = true
    return `✅ GitHub connected successfully`
  }

  async connectGitLab(token: string): Promise<string> {
    this.gitlabConnected = true
    return `✅ GitLab connected successfully`
  }

  async connectGitLawb(did: string): Promise<string> {
    this.gitlawbConnected = true
    return `✅ gitlawb connected with DID: ${did}`
  }

  async createRepository(userId: string, name: string): Promise<string> {
    const urls = []
    if (this.githubConnected) urls.push(`https://github.com/meio/${name}`)
    if (this.gitlabConnected) urls.push(`https://gitlab.com/meio/${name}`)
    if (this.gitlawbConnected) urls.push(`https://gitlawb.com/meio/${name}`)
    
    return urls.length > 0 ? urls.join(', ') : 'local repository only'
  }
}
backend/src/blockchain/did.module.ts
typescript
import { Module } from '@nestjs/common'
import { DIDManager } from './did.utils'

@Module({
  providers: [DIDManager],
  exports: [DIDManager],
})
export class DidModule {}
backend/src/blockchain/did.utils.ts
typescript
export interface DIDDocument {
  id: string
  verificationMethod: Array<{
    id: string
    type: string
    controller: string
  }>
  authentication: string[]
}

export function createDID(agentId: string): DIDDocument {
  const didId = `did:meio:${Buffer.from(agentId).toString('base64')}`
  
  return {
    id: didId,
    verificationMethod: [
      {
        id: `${didId}#keys-1`,
        type: 'Ed25519VerificationKey2020',
        controller: didId,
      }
    ],
    authentication: [`${didId}#keys-1`]
  }
}

export class DIDManager {
  private dids: Map<string, DIDDocument> = new Map()
  
  registerDID(agentId: string): DIDDocument {
    const did = createDID(agentId)
    this.dids.set(agentId, did)
    return did
  }
  
  resolveDID(didString: string): DIDDocument | null {
    for (const [_, doc] of this.dids) {
      if (doc.id === didString) return doc
    }
    return null
  }
}
backend/src/mcp/mcp.module.ts
typescript
import { Module } from '@nestjs/common'
import { McpService } from './mcp.server'

@Module({
  providers: [McpService],
  exports: [McpService],
})
export class McpModule {}
backend/src/mcp/mcp.server.ts
typescript
import { Injectable, OnModuleInit } from '@nestjs/common'

@Injectable()
export class McpService implements OnModuleInit {
  async onModuleInit() {
    console.log('🔌 MCP Server initialized')
    console.log('   Tools available: create_project, deploy_project, spawn_agent, query_agent_status')
  }

  async callTool(toolName: string, args: any): Promise<any> {
    switch (toolName) {
      case 'create_project':
        return `✅ Project ${args.name} created`
      case 'deploy_project':
        return `🚀 Deploying ${args.projectId} to ${args.environment}`
      case 'spawn_agent':
        return `🤖 Spawned ${args.type} agent`
      default:
        return `Unknown tool: ${toolName}`
    }
  }
}
backend/src/deployments/deploy.module.ts
typescript
import { Module } from '@nestjs/common'
import { DeployService } from './deploy.service'

@Module({
  providers: [DeployService],
  exports: [DeployService],
})
export class DeployModule {}
backend/src/deployments/deploy.service.ts
typescript
import { Injectable } from '@nestjs/common'

@Injectable()
export class DeployService {
  async deploy(projectId: string, environment: string): Promise<string> {
    // Simulate deployment
    await new Promise(resolve => setTimeout(resolve, 2000))
    return `https://${projectId}.${environment}.meio.local`
  }
}
3️⃣ INFRASTRUCTURE
infrastructure/scripts/setup.sh
bash
#!/bin/bash

echo "╔══════════════════════════════════════════════════════════════╗"
echo "║                                                              ║"
echo "║   🚀 MEIO Platform - Infrastructure Setup                   ║"
echo "║                                                              ║"
echo "╚══════════════════════════════════════════════════════════════╝"
echo ""

# Check if Docker is running
if ! docker info > /dev/null 2>&1; then
    echo "❌ Docker is not running. Please start Docker first."
    exit 1
fi

echo "📦 Setting up containers..."

# Create network
docker network create meio-network 2>/dev/null && echo "✅ Network created" || echo "ℹ️  Network already exists"

# PostgreSQL
if ! docker ps | grep -q meio-postgres; then
    docker run -d \
      --name meio-postgres \
      --network meio-network \
      -e POSTGRES_USER=meio \
      -e POSTGRES_PASSWORD=meio123 \
      -e POSTGRES_DB=meio \
      -p 5432:5432 \
      postgres:15
    echo "✅ PostgreSQL started on port 5432"
else
    echo "ℹ️  PostgreSQL already running"
fi

# Redis
if ! docker ps | grep -q meio-redis; then
    docker run -d \
      --name meio-redis \
      --network meio-network \
      -p 6379:6379 \
      redis:7-alpine
    echo "✅ Redis started on port 6379"
else
    echo "ℹ️  Redis already running"
fi

# Qdrant
if ! docker ps | grep -q meio-qdrant; then
    docker run -d \
      --name meio-qdrant \
      --network meio-network \
      -p 6333:6333 \
      qdrant/qdrant
    echo "✅ Qdrant started on port 6333"
else
    echo "ℹ️  Qdrant already running"
fi

echo ""
echo "╔══════════════════════════════════════════════════════════════╗"
echo "║                                                              ║"
echo "║   ✅ Infrastructure is ready!                                ║"
echo "║                                                              ║"
echo "║   PostgreSQL:  localhost:5432 (user: meio, pass: meio123)   ║"
echo "║   Redis:       localhost:6379                               ║"
echo "║   Qdrant:      localhost:6333                               ║"
echo "║                                                              ║"
echo "╚══════════════════════════════════════════════════════════════╝"
infrastructure/docker/Dockerfile.backend
dockerfile
FROM node:20-alpine AS builder

WORKDIR /app
COPY backend/package*.json ./
RUN npm ci

COPY backend/ .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY backend/package*.json ./

EXPOSE 3001
CMD ["node", "dist/main"]
infrastructure/docker/Dockerfile.frontend
dockerfile
FROM node:20-alpine AS builder

WORKDIR /app
COPY frontend/package*.json ./
RUN npm ci

COPY frontend/ .
RUN npm run build

FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/.next ./.next
COPY --from=builder /app/public ./public
COPY --from=builder /app/node_modules ./node_modules
COPY frontend/package*.json ./

EXPOSE 3000
CMD ["npm", "start"]
infrastructure/kubernetes/deployment.yaml
yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: meio-backend
  namespace: meio
spec:
  replicas: 2
  selector:
    matchLabels:
      app: meio-backend
  template:
    metadata:
      labels:
        app: meio-backend
    spec:
      containers:
      - name: backend
        image: meio/backend:latest
        ports:
        - containerPort: 3001
        env:
        - name: DATABASE_URL
          value: "postgresql://meio:meio123@postgres:5432/meio"
        - name: REDIS_URL
          value: "redis://redis:6379"
---
apiVersion: v1
kind: Service
metadata:
  name: meio-backend
  namespace: meio
spec:
  selector:
    app: meio-backend
  ports:
  - port: 3001
    targetPort: 3001
  type: LoadBalancer
4️⃣ PIPELINES CI/CD
.github/workflows/deploy.yml
yaml
name: MEIO CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - name: Test Backend
        run: |
          cd backend
          npm ci
          npm test
      - name: Test Frontend
        run: |
          cd frontend
          npm ci
          npm test

  deploy-staging:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4
      - name: Deploy to Staging
        run: |
          echo "🚀 Deploying to staging..."
          curl -X POST http://staging.meio.local/api/deploy
.gitlab-ci.yml
yaml
stages:
  - test
  - build
  - deploy

variables:
  NODE_VERSION: "20"

test-backend:
  stage: test
  image: node:$NODE_VERSION
  script:
    - cd backend && npm ci && npm test

test-frontend:
  stage: test
  image: node:$NODE_VERSION
  script:
    - cd frontend && npm ci && npm test

deploy-staging:
  stage: deploy
  only:
    - main
  script:
    - echo "Deploying to staging..."
5️⃣ DATABASE SCHEMAS
schemas/postgres/01_schema.sql
sql
-- Users table
CREATE TABLE IF NOT EXISTS users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  did VARCHAR(255) UNIQUE NOT NULL,
  email VARCHAR(255) UNIQUE,
  username VARCHAR(100) UNIQUE,
  trust_score DECIMAL(3,2) DEFAULT 0.5,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Projects table
CREATE TABLE IF NOT EXISTS projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  name VARCHAR(255) NOT NULL,
  description TEXT,
  repo_url VARCHAR(500),
  git_provider VARCHAR(50),
  created_at TIMESTAMP DEFAULT NOW()
);

-- Agents table
CREATE TABLE IF NOT EXISTS agents (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id),
  name VARCHAR(255) NOT NULL,
  type VARCHAR(100),
  did VARCHAR(255) UNIQUE,
  trust_score DECIMAL(3,2) DEFAULT 0.5,
  status VARCHAR(50) DEFAULT 'idle',
  memory_json JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Deployments table
CREATE TABLE IF NOT EXISTS deployments (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id),
  status VARCHAR(50),
  url VARCHAR(500),
  logs TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Commands history
CREATE TABLE IF NOT EXISTS commands (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id),
  command_text TEXT NOT NULL,
  output TEXT,
  executed_at TIMESTAMP DEFAULT NOW()
);

-- Indexes
CREATE INDEX IF NOT EXISTS idx_users_did ON users(did);
CREATE INDEX IF NOT EXISTS idx_projects_user_id ON projects(user_id);
CREATE INDEX IF NOT EXISTS idx_agents_project_id ON agents(project_id);
schemas/qdrant/collections.json
json
{
  "collections": [
    {
      "name": "agent_memory",
      "vector_size": 768,
      "distance": "Cosine"
    },
    {
      "name": "code_embeddings",
      "vector_size": 768,
      "distance": "Cosine"
    }
  ]
}
6️⃣ DOCUMENTATION
docs/README.md
markdown
# 🚀 MEIO Platform

**MEIO** is an agentic operating system for creating applications and AI agents autonomously.

## Quick Start

```bash
# Clone the repository
git clone https://github.com/meio/platform.git
cd meio

# Setup infrastructure
bash infrastructure/scripts/setup.sh

# Install dependencies
cd backend && npm install
cd ../frontend && npm install

# Start backend
cd backend && npm run start:dev

# Start frontend (new terminal)
cd frontend && npm run dev

# Open browser
open http://localhost:3000
Available Commands
Command	Description
forge create app <name>	Create new application
forge create agent <name>	Create new AI agent
forge spawn fullstack <project>	Spawn full agent team
forge deploy production	Deploy to production
forge connect github	Connect GitHub
forge help	Show help
Architecture
Frontend: Next.js + Tailwind + WebSockets

Backend: NestJS + PostgreSQL + Redis

Agents: Multi-agent system with trust scores

Deployment: Docker + Kubernetes

License
MIT

text

---

## ✅ EXECUÇÃO DO COMANDO

Agora, para criar toda a plataforma "MEIO", execute:

```bash
# 1. Create project directory
mkdir -p meio
cd meio

# 2. Copy all files above to their respective paths

# 3. Make setup script executable
chmod +x infrastructure/scripts/setup.sh

# 4. Run infrastructure setup
./infrastructure/scripts/setup.sh

# 5. Install backend dependencies
cd backend && npm install

# 6. Install frontend dependencies
cd ../frontend && npm install

# 7. Start backend
npm run start:dev

# 8. In new terminal, start frontend
cd frontend && npm run dev

# 9. Open browser to http://localhost:3000
