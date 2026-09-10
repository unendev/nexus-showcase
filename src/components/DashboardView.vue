<script setup lang="ts">
import { ref } from 'vue'
import { 
  DollarSign, 
  Activity, 
  Cpu, 
  Users, 
  ArrowUpRight, 
  CheckCircle2, 
  Clock, 
  Layers, 
  ShieldCheck, 
  Sparkles,
  RefreshCw
} from 'lucide-vue-next'

const timeRange = ref<'24h' | '7d' | '30d'>('7d')
const isRefreshing = ref(false)

const metrics = ref([
  {
    title: 'Active ARR Run-Rate',
    value: '$48,250',
    change: '+14.2%',
    isPositive: true,
    subtext: 'vs previous 30 days',
    icon: DollarSign,
    color: 'emerald'
  },
  {
    title: 'API Request Volume',
    value: '1,284,920',
    change: '+28.4%',
    isPositive: true,
    subtext: '99.98% uptime SLA',
    icon: Activity,
    color: 'cyan'
  },
  {
    title: 'LLM Token Efficiency',
    value: '42.8 tok/$',
    change: '+19.1%',
    isPositive: true,
    subtext: 'Semantic caching active',
    icon: Cpu,
    color: 'violet'
  },
  {
    title: 'Enterprise Tenants',
    value: '142',
    change: '+8',
    isPositive: true,
    subtext: 'Zero churn recorded',
    icon: Users,
    color: 'amber'
  }
])

const recentActivities = ref([
  {
    id: 'evt-9041',
    endpoint: '/v1/agent/intent/parse',
    status: 'Completed',
    latency: '142ms',
    tenant: 'Acme Corp (Pro)',
    model: 'Claude 3.5 Sonnet',
    cost: '$0.0034',
    time: '2 mins ago'
  },
  {
    id: 'evt-9040',
    endpoint: '/v1/workflows/dispatch',
    status: 'Completed',
    latency: '88ms',
    tenant: 'Apex Logistics',
    model: 'n8n Webhook Hook',
    cost: '$0.0008',
    time: '5 mins ago'
  },
  {
    id: 'evt-9039',
    endpoint: '/v1/vector/embeddings',
    status: 'Processing',
    latency: '210ms',
    tenant: 'FinTech Labs',
    model: 'OpenAI text-embed-3',
    cost: '$0.0002',
    time: '7 mins ago'
  },
  {
    id: 'evt-9038',
    endpoint: '/v1/database/cdc/sync',
    status: 'Completed',
    latency: '64ms',
    tenant: 'ScaleFlow Global',
    model: 'PostgreSQL Realtime',
    cost: '$0.0000',
    time: '12 mins ago'
  }
])

const handleRefresh = () => {
  isRefreshing.value = true
  setTimeout(() => {
    isRefreshing.value = false
  }, 600)
}
</script>

<template>
  <div class="space-y-6">
    <!-- View Header & Time Filter -->
    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 pb-2 border-b border-zinc-800/80">
      <div>
        <h2 class="text-xl font-bold text-zinc-100 flex items-center gap-2">
          <span>Enterprise SaaS Core Metrics</span>
          <span class="text-xs px-2 py-0.5 rounded-full bg-emerald-500/10 text-emerald-400 border border-emerald-500/20 font-mono">
            Production Cluster (us-east-1)
          </span>
        </h2>
        <p class="text-sm text-zinc-400 mt-0.5">
          Real-time transactional telemetry and revenue run-rate for multi-tenant deployments.
        </p>
      </div>

      <div class="flex items-center gap-2">
        <div class="bg-zinc-900 border border-zinc-800 rounded-lg p-1 flex items-center gap-1">
          <button 
            v-for="range in (['24h', '7d', '30d'] as const)" 
            :key="range"
            @click="timeRange = range"
            :class="[
              'px-3 py-1 rounded text-xs font-medium transition-all cursor-pointer',
              timeRange === range 
                ? 'bg-zinc-800 text-zinc-100 shadow-sm' 
                : 'text-zinc-400 hover:text-zinc-200'
            ]"
          >
            {{ range }}
          </button>
        </div>

        <button 
          @click="handleRefresh"
          :disabled="isRefreshing"
          class="p-2 rounded-lg bg-zinc-900 border border-zinc-800 text-zinc-400 hover:text-zinc-200 hover:bg-zinc-800 transition-colors cursor-pointer"
          title="Refresh Data"
        >
          <RefreshCw :class="['w-4 h-4', isRefreshing ? 'animate-spin text-emerald-400' : '']" />
        </button>
      </div>
    </div>

    <!-- KPI Metric Cards Grid -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
      <div 
        v-for="(item, idx) in metrics" 
        :key="idx"
        class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-5 hover:border-zinc-700/80 transition-all hover:shadow-lg hover:shadow-black/40 group"
      >
        <div class="flex items-center justify-between">
          <span class="text-xs font-medium text-zinc-400 uppercase tracking-wider">{{ item.title }}</span>
          <div class="w-8 h-8 rounded-lg bg-zinc-800/80 flex items-center justify-center text-zinc-300 group-hover:scale-110 transition-transform">
            <component :is="item.icon" class="w-4 h-4" />
          </div>
        </div>

        <div class="mt-4 flex items-baseline justify-between">
          <span class="text-2xl font-bold tracking-tight text-zinc-100 font-mono">{{ item.value }}</span>
          <span class="flex items-center text-xs font-semibold text-emerald-400">
            <ArrowUpRight class="w-3.5 h-3.5 mr-0.5" />
            {{ item.change }}
          </span>
        </div>

        <div class="mt-2 text-xs text-zinc-500 font-mono">
          {{ item.subtext }}
        </div>
      </div>
    </div>

    <!-- Chart & Architecture Showcase Section -->
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
      <!-- Left 2 Cols: Interactive Latency & Throughput Canvas -->
      <div class="lg:col-span-2 bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-6">
        <div class="flex items-center justify-between mb-4">
          <div>
            <h3 class="text-sm font-semibold text-zinc-200 flex items-center gap-2">
              <Layers class="w-4 h-4 text-cyan-400" />
              API Throughput & Latency Curve
            </h3>
            <p class="text-xs text-zinc-500 mt-0.5">Rolling average response time across edge gateways</p>
          </div>
          <div class="flex items-center gap-4 text-xs font-mono">
            <span class="flex items-center gap-1.5 text-zinc-400">
              <span class="w-2.5 h-2.5 rounded-full bg-cyan-400"></span> Requests/sec
            </span>
            <span class="flex items-center gap-1.5 text-zinc-400">
              <span class="w-2.5 h-2.5 rounded-full bg-violet-400"></span> p99 Latency (ms)
            </span>
          </div>
        </div>

        <!-- SVG Vector Chart Mockup -->
        <div class="relative h-56 w-full pt-4">
          <svg class="w-full h-full overflow-visible" viewBox="0 0 500 180" preserveAspectRatio="none">
            <defs>
              <linearGradient id="cyanGrad" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#22d3ee" stop-opacity="0.25" />
                <stop offset="100%" stop-color="#22d3ee" stop-opacity="0.0" />
              </linearGradient>
            </defs>

            <!-- Grid Lines -->
            <line x1="0" y1="30" x2="500" y2="30" stroke="#27272a" stroke-dasharray="4" />
            <line x1="0" y1="75" x2="500" y2="75" stroke="#27272a" stroke-dasharray="4" />
            <line x1="0" y1="120" x2="500" y2="120" stroke="#27272a" stroke-dasharray="4" />
            <line x1="0" y1="165" x2="500" y2="165" stroke="#27272a" stroke-dasharray="4" />

            <!-- Cyan Area & Line (Throughput) -->
            <path 
              d="M0,130 C70,110 120,60 180,75 C240,90 300,40 370,55 C430,70 470,25 500,35 L500,175 L0,175 Z" 
              fill="url(#cyanGrad)" 
            />
            <path 
              d="M0,130 C70,110 120,60 180,75 C240,90 300,40 370,55 C430,70 470,25 500,35" 
              fill="none" 
              stroke="#22d3ee" 
              stroke-width="2.5" 
            />

            <!-- Violet Line (Latency) -->
            <path 
              d="M0,150 C80,140 140,115 200,120 C260,125 320,95 380,105 C440,115 480,85 500,90" 
              fill="none" 
              stroke="#a78bfa" 
              stroke-width="2" 
              stroke-dasharray="2" 
            />
          </svg>

          <div class="flex justify-between text-[11px] font-mono text-zinc-500 mt-2">
            <span>00:00</span>
            <span>04:00</span>
            <span>08:00</span>
            <span>12:00</span>
            <span>16:00</span>
            <span>20:00</span>
            <span>Live</span>
          </div>
        </div>
      </div>

      <!-- Right 1 Col: Production Health & Security Stack -->
      <div class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-6 flex flex-col justify-between">
        <div>
          <h3 class="text-sm font-semibold text-zinc-200 flex items-center gap-2 mb-4">
            <ShieldCheck class="w-4 h-4 text-emerald-400" />
            Infrastructure Status
          </h3>

          <div class="space-y-3">
            <div class="flex items-center justify-between p-2.5 rounded-lg bg-zinc-950/60 border border-zinc-800">
              <div class="flex items-center gap-2">
                <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
                <span class="text-xs font-medium text-zinc-300">Authentication & RBAC</span>
              </div>
              <span class="text-[11px] font-mono text-zinc-400">JWT + OAuth 2.0</span>
            </div>

            <div class="flex items-center justify-between p-2.5 rounded-lg bg-zinc-950/60 border border-zinc-800">
              <div class="flex items-center gap-2">
                <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
                <span class="text-xs font-medium text-zinc-300">Database Connection Pool</span>
              </div>
              <span class="text-[11px] font-mono text-zinc-400">Supabase / Prisma</span>
            </div>

            <div class="flex items-center justify-between p-2.5 rounded-lg bg-zinc-950/60 border border-zinc-800">
              <div class="flex items-center gap-2">
                <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
                <span class="text-xs font-medium text-zinc-300">Global Edge Cache</span>
              </div>
              <span class="text-[11px] font-mono text-zinc-400">Cloudflare Workers</span>
            </div>

            <div class="flex items-center justify-between p-2.5 rounded-lg bg-zinc-950/60 border border-zinc-800">
              <div class="flex items-center gap-2">
                <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
                <span class="text-xs font-medium text-zinc-300">AI Fallback Routing</span>
              </div>
              <span class="text-[11px] font-mono text-zinc-400">Anthropic + OpenAI</span>
            </div>
          </div>
        </div>

        <div class="mt-6 pt-4 border-t border-zinc-800 text-xs text-zinc-500 flex items-center justify-between">
          <span>Target Delivery Time</span>
          <span class="text-zinc-200 font-semibold font-mono">3 to 7 Business Days</span>
        </div>
      </div>
    </div>

    <!-- Live Transactional Activity Log Table -->
    <div class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl overflow-hidden">
      <div class="p-5 border-b border-zinc-800 flex items-center justify-between">
        <div>
          <h3 class="text-sm font-semibold text-zinc-200 flex items-center gap-2">
            <Sparkles class="w-4 h-4 text-amber-400" />
            Live Ingestion & Execution Telemetry
          </h3>
          <p class="text-xs text-zinc-500 mt-0.5">Asynchronous event stream captured via serverless workers</p>
        </div>
        <span class="text-xs font-mono text-zinc-400 bg-zinc-800/80 px-2.5 py-1 rounded">
          Sync Rate: 1.4k events/min
        </span>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full text-left text-xs">
          <thead class="bg-zinc-950/60 text-zinc-400 font-mono border-b border-zinc-800">
            <tr>
              <th class="px-5 py-3">Event ID</th>
              <th class="px-5 py-3">Route Endpoint</th>
              <th class="px-5 py-3">Tenant</th>
              <th class="px-5 py-3">Engine / Handler</th>
              <th class="px-5 py-3">Latency</th>
              <th class="px-5 py-3">Status</th>
              <th class="px-5 py-3 text-right">Time</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-zinc-800/60 font-mono">
            <tr v-for="evt in recentActivities" :key="evt.id" class="hover:bg-zinc-800/40 transition-colors">
              <td class="px-5 py-3.5 text-zinc-400">{{ evt.id }}</td>
              <td class="px-5 py-3.5 font-medium text-cyan-400">{{ evt.endpoint }}</td>
              <td class="px-5 py-3.5 text-zinc-300">{{ evt.tenant }}</td>
              <td class="px-5 py-3.5 text-zinc-400">{{ evt.model }}</td>
              <td class="px-5 py-3.5 text-zinc-300">{{ evt.latency }}</td>
              <td class="px-5 py-3.5">
                <span 
                  :class="[
                    'inline-flex items-center gap-1 px-2 py-0.5 rounded-full text-[11px] font-sans font-medium',
                    evt.status === 'Completed' 
                      ? 'bg-emerald-500/10 text-emerald-400 border border-emerald-500/20' 
                      : 'bg-amber-500/10 text-amber-400 border border-amber-500/20'
                  ]"
                >
                  <CheckCircle2 v-if="evt.status === 'Completed'" class="w-3 h-3" />
                  <Clock v-else class="w-3 h-3 animate-spin" />
                  {{ evt.status }}
                </span>
              </td>
              <td class="px-5 py-3.5 text-right text-zinc-500">{{ evt.time }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
