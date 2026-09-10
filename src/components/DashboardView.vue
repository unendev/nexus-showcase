<script setup lang="ts">
import { ref, computed } from 'vue'
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
  RefreshCw,
  SlidersHorizontal
} from 'lucide-vue-next'

type IndustryType = 'fintech' | 'ecommerce' | 'logistics'
type TimeRangeType = '24h' | '7d' | '30d'

const selectedIndustry = ref<IndustryType>('fintech')
const timeRange = ref<TimeRangeType>('7d')
const isRefreshing = ref(false)

const industryPresets: Record<IndustryType, {
  name: string
  badge: string
  metrics: Array<{ title: string; value: string; change: string; subtext: string; icon: any; color: string }>
  curvePoints: { throughput: string; latency: string }
  activities: Array<{ id: string; endpoint: string; status: string; latency: string; tenant: string; model: string; time: string }>
}> = {
  fintech: {
    name: 'FinTech SaaS',
    badge: 'Real-time Ledger Cluster',
    metrics: [
      { title: 'Active ARR Run-Rate', value: '$124,500', change: '+18.4%', subtext: 'Quarterly expansion', icon: DollarSign, color: 'emerald' },
      { title: 'Payment Ingestion Rate', value: '3,842 req/s', change: '+24.1%', subtext: 'Zero-failure idempotent queue', icon: Activity, color: 'cyan' },
      { title: 'KYC Settlement SLA', value: '420ms', change: '-32.0%', subtext: 'Automated webhook dispatch', icon: Cpu, color: 'violet' },
      { title: 'Compliant Tenants', value: '86', change: '+5', subtext: 'SOC2 Type II active', icon: Users, color: 'amber' }
    ],
    curvePoints: {
      throughput: "M0,130 C70,110 120,60 180,75 C240,90 300,40 370,55 C430,70 470,25 500,35",
      latency: "M0,150 C80,140 140,115 200,120 C260,125 320,95 380,105 C440,115 480,85 500,90"
    },
    activities: [
      { id: 'tx-8821', endpoint: '/v1/settlements/ach', status: 'Completed', latency: '94ms', tenant: 'Apex Pay UK', model: 'Stripe Direct Gateway', time: '1 min ago' },
      { id: 'tx-8820', endpoint: '/v1/kyc/verify/identity', status: 'Completed', latency: '310ms', tenant: 'Vanguard Capital', model: 'Persona KYC Pipeline', time: '3 mins ago' },
      { id: 'tx-8819', endpoint: '/v1/ledger/reconcile', status: 'Processing', latency: '142ms', tenant: 'BridgePoint Fin', model: 'PostgreSQL Realtime CDC', time: '6 mins ago' },
      { id: 'tx-8818', endpoint: '/v1/fraud/anomaly/eval', status: 'Completed', latency: '48ms', tenant: 'Nordic Bank Labs', model: 'Isolated Isolation Forest', time: '9 mins ago' }
    ]
  },
  ecommerce: {
    name: 'E-Commerce B2B',
    badge: 'Multi-Tenant Storefront Grid',
    metrics: [
      { title: 'Gross Merchandise Val', value: '$642,800', change: '+22.6%', subtext: '30-day cross-border GMV', icon: DollarSign, color: 'emerald' },
      { title: 'Cart Checkout Throughput', value: '1,420 req/s', change: '+15.8%', subtext: 'Redis distributed cache', icon: Activity, color: 'cyan' },
      { title: 'Inventory Sync Delay', value: '18ms', change: '-45.2%', subtext: 'Supabase Realtime Sync', icon: Cpu, color: 'violet' },
      { title: 'Connected Brands', value: '312', change: '+19', subtext: 'Multi-warehouse routing', icon: Users, color: 'amber' }
    ],
    curvePoints: {
      throughput: "M0,150 C90,120 160,80 230,95 C300,110 360,50 420,65 C460,80 480,45 500,40",
      latency: "M0,160 C70,145 150,130 220,135 C300,140 370,110 420,120 C460,125 480,95 500,100"
    },
    activities: [
      { id: 'ord-5012', endpoint: '/v2/orders/checkout/fast', status: 'Completed', latency: '62ms', tenant: 'SoleMarket NYC', model: 'Shopify Storefront API', time: '2 mins ago' },
      { id: 'ord-5011', endpoint: '/v2/inventory/warehouse/sync', status: 'Completed', latency: '35ms', tenant: 'Aura Logistics', model: 'Redis PubSub Worker', time: '4 mins ago' },
      { id: 'ord-5010', endpoint: '/v2/recommendations/vector', status: 'Completed', latency: '128ms', tenant: 'Luxe Goods FR', model: 'Pinecone Similarity Search', time: '8 mins ago' },
      { id: 'ord-5009', endpoint: '/v2/tax/vat/calculate', status: 'Processing', latency: '88ms', tenant: 'Berlin Apparel GmbH', model: 'TaxJar Automated Engine', time: '11 mins ago' }
    ]
  },
  logistics: {
    name: 'AI Logistics & Freight',
    badge: 'Automated Routing DAG',
    metrics: [
      { title: 'Fleet Freight Under Mgmt', value: '$389,000', change: '+12.9%', subtext: 'Container volume MoM', icon: DollarSign, color: 'emerald' },
      { title: 'Quote Ingestion Volume', value: '890 quotes/hr', change: '+31.4%', subtext: 'Automated OCR & email parse', icon: Activity, color: 'cyan' },
      { title: 'Carrier Routing Latency', value: '68ms', change: '-19.5%', subtext: 'n8n Webhook Ingestion', icon: Cpu, color: 'violet' },
      { title: 'Global Forwarders', value: '64', change: '+7', subtext: 'Rotterdam / Singapore / LA', icon: Users, color: 'amber' }
    ],
    curvePoints: {
      throughput: "M0,140 C80,130 140,70 210,85 C280,100 340,30 400,45 C450,60 480,20 500,30",
      latency: "M0,145 C70,135 150,105 210,115 C280,120 350,90 410,100 C450,110 480,80 500,85"
    },
    activities: [
      { id: 'frt-1092', endpoint: '/v1/quotes/extract/pdf', status: 'Completed', latency: '420ms', tenant: 'Maersk Regional', model: 'Claude 3.5 PDF Document Parser', time: '3 mins ago' },
      { id: 'frt-1091', endpoint: '/v1/dispatch/customs/entry', status: 'Completed', latency: '82ms', tenant: 'Hamburg Express', model: 'Port Authority Gateway', time: '5 mins ago' },
      { id: 'frt-1090', endpoint: '/v1/containers/telemetry/iot', status: 'Processing', latency: '54ms', tenant: 'Pacific Cargo Line', model: 'MQTT Ingestion Broker', time: '9 mins ago' },
      { id: 'frt-1089', endpoint: '/v1/slack/notify/critical', status: 'Completed', latency: '38ms', tenant: 'Rotterdam Hub', model: 'Slack Webhook Bot', time: '14 mins ago' }
    ]
  }
}

const currentData = computed(() => industryPresets[selectedIndustry.value])

const handleRefresh = () => {
  isRefreshing.value = true
  setTimeout(() => {
    isRefreshing.value = false
  }, 450)
}
</script>

<template>
  <div class="space-y-6">
    <!-- View Header & Interactive Industry Preset Controls -->
    <div class="flex flex-col lg:flex-row lg:items-center justify-between gap-4 pb-2 border-b border-zinc-800/80">
      <div>
        <div class="flex items-center gap-2">
          <h2 class="text-lg font-bold text-zinc-100 font-mono tracking-tight">System Metrics</h2>
          <span class="text-xs px-2 py-0.5 rounded bg-zinc-800 text-zinc-300 border border-zinc-700 font-mono">
            {{ currentData.badge }}
          </span>
        </div>
        <p class="text-xs text-zinc-400 mt-0.5 font-mono">
          Asynchronous multi-tenant telemetry and revenue run-rate.
        </p>
      </div>

      <!-- Controls Row: Industry Switcher + Time Range -->
      <div class="flex flex-wrap items-center gap-2.5">
        <!-- Industry Preset Selector -->
        <div class="bg-zinc-900 border border-zinc-800 rounded-lg p-1 flex items-center gap-1">
          <span class="px-2 text-[11px] font-mono text-zinc-500 hidden sm:inline flex items-center gap-1">
            <SlidersHorizontal class="w-3 h-3" /> Preset:
          </span>
          <button 
            v-for="presetKey in (['fintech', 'ecommerce', 'logistics'] as const)" 
            :key="presetKey"
            @click="selectedIndustry = presetKey"
            :class="[
              'px-2.5 py-1 rounded text-xs font-mono font-medium transition-all cursor-pointer',
              selectedIndustry === presetKey 
                ? 'bg-zinc-800 text-emerald-400 border border-zinc-700 shadow-sm' 
                : 'text-zinc-400 hover:text-zinc-200'
            ]"
          >
            {{ industryPresets[presetKey].name }}
          </button>
        </div>

        <!-- Time Range Selector -->
        <div class="bg-zinc-900 border border-zinc-800 rounded-lg p-1 flex items-center gap-1">
          <button 
            v-for="range in (['24h', '7d', '30d'] as const)" 
            :key="range"
            @click="timeRange = range"
            :class="[
              'px-2.5 py-1 rounded text-xs font-mono transition-all cursor-pointer',
              timeRange === range 
                ? 'bg-zinc-800 text-zinc-100 shadow-sm' 
                : 'text-zinc-400 hover:text-zinc-200'
            ]"
          >
            {{ range }}
          </button>
        </div>

        <!-- Refresh Button -->
        <button 
          @click="handleRefresh"
          :disabled="isRefreshing"
          class="p-2 rounded-lg bg-zinc-900 border border-zinc-800 text-zinc-400 hover:text-zinc-200 hover:bg-zinc-800 transition-colors cursor-pointer"
          title="Refresh Metrics"
        >
          <RefreshCw :class="['w-3.5 h-3.5', isRefreshing ? 'animate-spin text-emerald-400' : '']" />
        </button>
      </div>
    </div>

    <!-- Dynamic KPI Cards Grid -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
      <div 
        v-for="(item, idx) in currentData.metrics" 
        :key="idx"
        class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-4.5 hover:border-zinc-700/80 transition-all hover:shadow-lg hover:shadow-black/40 group"
      >
        <div class="flex items-center justify-between">
          <span class="text-[11px] font-mono text-zinc-400 uppercase tracking-wider">{{ item.title }}</span>
          <div class="w-7 h-7 rounded-lg bg-zinc-800/80 flex items-center justify-center text-zinc-300 group-hover:scale-110 transition-transform">
            <component :is="item.icon" class="w-3.5 h-3.5" />
          </div>
        </div>

        <div class="mt-3 flex items-baseline justify-between">
          <span class="text-xl font-bold tracking-tight text-zinc-100 font-mono">{{ item.value }}</span>
          <span class="flex items-center text-xs font-mono font-semibold text-emerald-400">
            <ArrowUpRight class="w-3.5 h-3.5 mr-0.5" />
            {{ item.change }}
          </span>
        </div>

        <div class="mt-1.5 text-[11px] text-zinc-500 font-mono">
          {{ item.subtext }}
        </div>
      </div>
    </div>

    <!-- Chart & Infrastructure Stack -->
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
      <!-- SVG Vector Chart -->
      <div class="lg:col-span-2 bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-5">
        <div class="flex items-center justify-between mb-3">
          <div>
            <h3 class="text-xs font-mono font-semibold text-zinc-200 flex items-center gap-2">
              <Layers class="w-3.5 h-3.5 text-cyan-400" />
              Throughput & Latency Spectrum
            </h3>
            <p class="text-[11px] font-mono text-zinc-500 mt-0.5">Preset: {{ currentData.name }} (P99 edge monitoring)</p>
          </div>
          <div class="flex items-center gap-4 text-[11px] font-mono">
            <span class="flex items-center gap-1.5 text-zinc-400">
              <span class="w-2 h-2 rounded-full bg-cyan-400"></span> Ingestion
            </span>
            <span class="flex items-center gap-1.5 text-zinc-400">
              <span class="w-2 h-2 rounded-full bg-violet-400"></span> Latency
            </span>
          </div>
        </div>

        <!-- SVG Vector Canvas with Dynamic Path -->
        <div class="relative h-48 w-full pt-2">
          <svg class="w-full h-full overflow-visible" viewBox="0 0 500 180" preserveAspectRatio="none">
            <defs>
              <linearGradient id="cyanGrad" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#22d3ee" stop-opacity="0.22" />
                <stop offset="100%" stop-color="#22d3ee" stop-opacity="0.0" />
              </linearGradient>
            </defs>

            <!-- Grid Lines -->
            <line x1="0" y1="30" x2="500" y2="30" stroke="#27272a" stroke-dasharray="3" />
            <line x1="0" y1="75" x2="500" y2="75" stroke="#27272a" stroke-dasharray="3" />
            <line x1="0" y1="120" x2="500" y2="120" stroke="#27272a" stroke-dasharray="3" />
            <line x1="0" y1="165" x2="500" y2="165" stroke="#27272a" stroke-dasharray="3" />

            <!-- Dynamic Path Ingestion -->
            <path 
              :d="currentData.curvePoints.throughput + ' L500,175 L0,175 Z'" 
              fill="url(#cyanGrad)" 
              class="transition-all duration-500"
            />
            <path 
              :d="currentData.curvePoints.throughput" 
              fill="none" 
              stroke="#22d3ee" 
              stroke-width="2" 
              class="transition-all duration-500"
            />

            <!-- Dynamic Path Latency -->
            <path 
              :d="currentData.curvePoints.latency" 
              fill="none" 
              stroke="#a78bfa" 
              stroke-width="1.8" 
              stroke-dasharray="2" 
              class="transition-all duration-500"
            />
          </svg>

          <div class="flex justify-between text-[10px] font-mono text-zinc-500 mt-2">
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

      <!-- Infrastructure Status Component -->
      <div class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-5 flex flex-col justify-between">
        <div>
          <h3 class="text-xs font-mono font-semibold text-zinc-200 flex items-center gap-2 mb-3">
            <ShieldCheck class="w-3.5 h-3.5 text-emerald-400" />
            Production Topology
          </h3>

          <div class="space-y-2.5 text-xs font-mono">
            <div class="flex items-center justify-between p-2 rounded bg-zinc-950/60 border border-zinc-800/80">
              <div class="flex items-center gap-2">
                <span class="w-1.5 h-1.5 rounded-full bg-emerald-400"></span>
                <span class="text-zinc-300">Auth & RBAC</span>
              </div>
              <span class="text-[11px] text-zinc-400">JWT + OAuth 2.0</span>
            </div>

            <div class="flex items-center justify-between p-2 rounded bg-zinc-950/60 border border-zinc-800/80">
              <div class="flex items-center gap-2">
                <span class="w-1.5 h-1.5 rounded-full bg-emerald-400"></span>
                <span class="text-zinc-300">Database Engine</span>
              </div>
              <span class="text-[11px] text-zinc-400">PostgreSQL / Supabase</span>
            </div>

            <div class="flex items-center justify-between p-2 rounded bg-zinc-950/60 border border-zinc-800/80">
              <div class="flex items-center gap-2">
                <span class="w-1.5 h-1.5 rounded-full bg-emerald-400"></span>
                <span class="text-zinc-300">Edge Caching</span>
              </div>
              <span class="text-[11px] text-zinc-400">Cloudflare Workers</span>
            </div>

            <div class="flex items-center justify-between p-2 rounded bg-zinc-950/60 border border-zinc-800/80">
              <div class="flex items-center gap-2">
                <span class="w-1.5 h-1.5 rounded-full bg-emerald-400"></span>
                <span class="text-zinc-300">LLM Fallback</span>
              </div>
              <span class="text-[11px] text-zinc-400">Claude 3.5 / OpenAI</span>
            </div>
          </div>
        </div>

        <div class="mt-4 pt-3 border-t border-zinc-800/80 text-[11px] font-mono text-zinc-500 flex items-center justify-between">
          <span>Target Delivery</span>
          <span class="text-zinc-300 font-semibold">3 to 7 Days MVP</span>
        </div>
      </div>
    </div>

    <!-- Live Table -->
    <div class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl overflow-hidden">
      <div class="p-4 border-b border-zinc-800/80 flex items-center justify-between">
        <div class="flex items-center gap-2">
          <Sparkles class="w-3.5 h-3.5 text-amber-400" />
          <h3 class="text-xs font-mono font-semibold text-zinc-200">
            Live Stream Log ({{ currentData.name }})
          </h3>
        </div>
        <span class="text-[10px] font-mono text-zinc-400 bg-zinc-800/80 px-2 py-0.5 rounded">
          Sync Rate: 1.4k events/min
        </span>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full text-left text-xs">
          <thead class="bg-zinc-950/60 text-zinc-400 font-mono border-b border-zinc-800 text-[11px]">
            <tr>
              <th class="px-4 py-2.5">ID</th>
              <th class="px-4 py-2.5">Endpoint</th>
              <th class="px-4 py-2.5">Tenant</th>
              <th class="px-4 py-2.5">Gateway / Handler</th>
              <th class="px-4 py-2.5">Latency</th>
              <th class="px-4 py-2.5">Status</th>
              <th class="px-4 py-2.5 text-right">Time</th>
            </tr>
          </thead>
          <tbody class="divide-y divide-zinc-800/60 font-mono text-[11px]">
            <tr v-for="evt in currentData.activities" :key="evt.id" class="hover:bg-zinc-800/40 transition-colors">
              <td class="px-4 py-2.5 text-zinc-400">{{ evt.id }}</td>
              <td class="px-4 py-2.5 font-medium text-cyan-400">{{ evt.endpoint }}</td>
              <td class="px-4 py-2.5 text-zinc-300">{{ evt.tenant }}</td>
              <td class="px-4 py-2.5 text-zinc-400">{{ evt.model }}</td>
              <td class="px-4 py-2.5 text-zinc-300">{{ evt.latency }}</td>
              <td class="px-4 py-2.5">
                <span 
                  :class="[
                    'inline-flex items-center gap-1 px-2 py-0.5 rounded-full text-[10px]',
                    evt.status === 'Completed' 
                      ? 'bg-emerald-500/10 text-emerald-400 border border-emerald-500/20' 
                      : 'bg-amber-500/10 text-amber-400 border border-amber-500/20'
                  ]"
                >
                  <CheckCircle2 v-if="evt.status === 'Completed'" class="w-2.5 h-2.5" />
                  <Clock v-else class="w-2.5 h-2.5 animate-spin" />
                  {{ evt.status }}
                </span>
              </td>
              <td class="px-4 py-2.5 text-right text-zinc-500">{{ evt.time }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
