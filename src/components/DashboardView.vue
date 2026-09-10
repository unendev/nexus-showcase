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

const timeMultipliers: Record<TimeRangeType, { arr: number; requests: number; changeText: string; timeLabels: string[] }> = {
  '24h': { arr: 0.14, requests: 0.18, changeText: 'vs yesterday', timeLabels: ['00:00', '04:00', '08:00', '12:00', '16:00', '20:00', 'Live'] },
  '7d': { arr: 1.0, requests: 1.0, changeText: 'vs previous week', timeLabels: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'] },
  '30d': { arr: 4.2, requests: 4.5, changeText: 'vs previous month', timeLabels: ['W1', 'W2', 'W3', 'W4', 'W5', 'W6', 'Month End'] }
}

const industryPresets: Record<IndustryType, {
  name: string
  badge: string
  baseArr: number
  baseRequests: number
  tokenMetric: { label: string; value: string; change: string; subtext: string }
  tenantsMetric: { label: string; value: string; change: string; subtext: string }
  curvePointsByTime: Record<TimeRangeType, { throughput: string; latency: string }>
  activities: Array<{ id: string; endpoint: string; status: string; latency: string; tenant: string; model: string; time: string }>
}> = {
  fintech: {
    name: 'FinTech Core',
    badge: 'Real-time Ledger Cluster',
    baseArr: 124500,
    baseRequests: 384200,
    tokenMetric: { label: 'KYC Verification SLA', value: '420ms', change: '-32.0%', subtext: 'Automated webhook dispatch' },
    tenantsMetric: { label: 'Compliant Tenants', value: '86', change: '+5', subtext: 'SOC2 Type II active' },
    curvePointsByTime: {
      '24h': {
        throughput: "M0,140 C80,130 140,70 210,85 C280,100 340,30 400,45 C450,60 480,20 500,30",
        latency: "M0,145 C70,135 150,105 210,115 C280,120 350,90 410,100 C450,110 480,80 500,85"
      },
      '7d': {
        throughput: "M0,130 C70,110 120,60 180,75 C240,90 300,40 370,55 C430,70 470,25 500,35",
        latency: "M0,150 C80,140 140,115 200,120 C260,125 320,95 380,105 C440,115 480,85 500,90"
      },
      '30d': {
        throughput: "M0,160 C90,140 170,100 240,110 C310,120 380,50 430,60 C470,70 490,20 500,25",
        latency: "M0,135 C80,130 150,110 220,115 C290,120 360,85 420,90 C460,95 485,75 500,80"
      }
    },
    activities: [
      { id: 'tx-8821', endpoint: '/v1/settlements/ach', status: 'Completed', latency: '94ms', tenant: 'Apex Pay UK', model: 'Stripe Direct Gateway', time: '1 min ago' },
      { id: 'tx-8820', endpoint: '/v1/kyc/verify/identity', status: 'Completed', latency: '310ms', tenant: 'Vanguard Capital', model: 'Persona KYC Pipeline', time: '3 mins ago' },
      { id: 'tx-8819', endpoint: '/v1/ledger/reconcile', status: 'Processing', latency: '142ms', tenant: 'BridgePoint Fin', model: 'PostgreSQL Realtime CDC', time: '6 mins ago' },
      { id: 'tx-8818', endpoint: '/v1/fraud/anomaly/eval', status: 'Completed', latency: '48ms', tenant: 'Nordic Bank Labs', model: 'Isolation Forest Node', time: '9 mins ago' }
    ]
  },
  ecommerce: {
    name: 'E-Commerce B2B',
    badge: 'Storefront Mesh Grid',
    baseArr: 642800,
    baseRequests: 1420000,
    tokenMetric: { label: 'Inventory Sync Delay', value: '18ms', change: '-45.2%', subtext: 'Supabase Realtime Sync' },
    tenantsMetric: { label: 'Connected Brands', value: '312', change: '+19', subtext: 'Multi-warehouse routing' },
    curvePointsByTime: {
      '24h': {
        throughput: "M0,150 C70,130 130,90 200,100 C270,110 330,40 390,50 C440,65 470,30 500,45",
        latency: "M0,155 C80,140 160,120 230,125 C300,130 370,100 430,110 C460,115 480,90 500,95"
      },
      '7d': {
        throughput: "M0,150 C90,120 160,80 230,95 C300,110 360,50 420,65 C460,80 480,45 500,40",
        latency: "M0,160 C70,145 150,130 220,135 C300,140 370,110 420,120 C460,125 480,95 500,100"
      },
      '30d': {
        throughput: "M0,170 C100,130 180,70 250,85 C320,100 390,35 440,45 C470,55 490,25 500,30",
        latency: "M0,150 C90,135 170,115 240,120 C310,125 380,95 430,105 C470,110 485,85 500,90"
      }
    },
    activities: [
      { id: 'ord-5012', endpoint: '/v2/orders/checkout/fast', status: 'Completed', latency: '62ms', tenant: 'SoleMarket NYC', model: 'Shopify Storefront API', time: '2 mins ago' },
      { id: 'ord-5011', endpoint: '/v2/inventory/warehouse/sync', status: 'Completed', latency: '35ms', tenant: 'Aura Logistics', model: 'Redis PubSub Worker', time: '4 mins ago' },
      { id: 'ord-5010', endpoint: '/v2/recommendations/vector', status: 'Completed', latency: '128ms', tenant: 'Luxe Goods FR', model: 'Pinecone Vector Search', time: '8 mins ago' },
      { id: 'ord-5009', endpoint: '/v2/tax/vat/calculate', status: 'Processing', latency: '88ms', tenant: 'Berlin Apparel GmbH', model: 'TaxJar Automated Engine', time: '11 mins ago' }
    ]
  },
  logistics: {
    name: 'AI Logistics DAG',
    badge: 'Automated Freight DAG',
    baseArr: 389000,
    baseRequests: 890000,
    tokenMetric: { label: 'Carrier Routing SLA', value: '68ms', change: '-19.5%', subtext: 'n8n Webhook Ingestion' },
    tenantsMetric: { label: 'Global Forwarders', value: '64', change: '+7', subtext: 'Rotterdam / Singapore / LA' },
    curvePointsByTime: {
      '24h': {
        throughput: "M0,135 C70,120 130,80 190,95 C250,110 320,40 380,55 C430,70 470,25 500,35",
        latency: "M0,160 C80,145 150,125 210,130 C280,135 340,100 400,110 C440,115 480,85 500,90"
      },
      '7d': {
        throughput: "M0,140 C80,130 140,70 210,85 C280,100 340,30 400,45 C450,60 480,20 500,30",
        latency: "M0,145 C70,135 150,105 210,115 C280,120 350,90 410,100 C450,110 480,80 500,85"
      },
      '30d': {
        throughput: "M0,155 C90,115 160,60 230,75 C300,90 370,25 420,35 C460,50 485,15 500,20",
        latency: "M0,140 C80,130 160,100 220,105 C290,110 360,80 420,90 C460,95 480,70 500,75"
      }
    },
    activities: [
      { id: 'frt-1092', endpoint: '/v1/quotes/extract/pdf', status: 'Completed', latency: '420ms', tenant: 'Maersk Regional', model: 'Claude 3.5 Document Parser', time: '3 mins ago' },
      { id: 'frt-1091', endpoint: '/v1/dispatch/customs/entry', status: 'Completed', latency: '82ms', tenant: 'Hamburg Express', model: 'Port Authority Gateway', time: '5 mins ago' },
      { id: 'frt-1090', endpoint: '/v1/containers/telemetry/iot', status: 'Processing', latency: '54ms', tenant: 'Pacific Cargo Line', model: 'MQTT Ingestion Broker', time: '9 mins ago' },
      { id: 'frt-1089', endpoint: '/v1/slack/notify/critical', status: 'Completed', latency: '38ms', tenant: 'Rotterdam Hub', model: 'Slack Webhook Bot', time: '14 mins ago' }
    ]
  }
}

const currentPreset = computed(() => industryPresets[selectedIndustry.value])
const currentTimeMultiplier = computed(() => timeMultipliers[timeRange.value])

const dynamicMetrics = computed(() => {
  const mult = currentTimeMultiplier.value
  const preset = currentPreset.value
  const calculatedArr = Math.round(preset.baseArr * mult.arr)
  const calculatedReqs = Math.round(preset.baseRequests * mult.requests)

  return [
    {
      title: 'Active ARR Volume',
      value: '$' + calculatedArr.toLocaleString(),
      change: '+18.4%',
      subtext: mult.changeText,
      icon: DollarSign
    },
    {
      title: 'Ingestion Throughput',
      value: calculatedReqs.toLocaleString() + ' reqs',
      change: '+24.1%',
      subtext: 'Zero-failure rate',
      icon: Activity
    },
    {
      title: preset.tokenMetric.label,
      value: preset.tokenMetric.value,
      change: preset.tokenMetric.change,
      subtext: preset.tokenMetric.subtext,
      icon: Cpu
    },
    {
      title: preset.tenantsMetric.label,
      value: preset.tenantsMetric.value,
      change: preset.tenantsMetric.change,
      subtext: preset.tenantsMetric.subtext,
      icon: Users
    }
  ]
})

const dynamicCurve = computed(() => {
  return currentPreset.value.curvePointsByTime[timeRange.value]
})

const handleRefresh = () => {
  isRefreshing.value = true
  setTimeout(() => {
    isRefreshing.value = false
  }, 450)
}
</script>

<template>
  <div class="space-y-6">
    <!-- View Header & Interactive Controls -->
    <div class="flex flex-col lg:flex-row lg:items-center justify-between gap-4 pb-2 border-b border-zinc-200 dark:border-zinc-800/80">
      <div>
        <div class="flex items-center gap-2">
          <h2 class="text-base font-bold text-zinc-900 dark:text-zinc-100 font-mono tracking-tight">System Telemetry</h2>
          <span class="text-xs px-2 py-0.5 rounded bg-zinc-100 dark:bg-zinc-800 text-zinc-600 dark:text-zinc-300 border border-zinc-200 dark:border-zinc-700 font-mono">
            {{ currentPreset.badge }}
          </span>
        </div>
        <p class="text-xs text-zinc-500 dark:text-zinc-400 mt-0.5 font-mono">
          Live multi-tenant telemetry and revenue run-rate (Scope: {{ timeRange }}).
        </p>
      </div>

      <!-- Controls Row: Industry Switcher + Time Range -->
      <div class="flex flex-wrap items-center gap-2.5">
        <!-- Industry Selector -->
        <div class="bg-zinc-100 dark:bg-zinc-900 border border-zinc-200 dark:border-zinc-800 rounded-lg p-1 flex items-center gap-1">
          <span class="px-2 text-[11px] font-mono text-zinc-500 hidden sm:inline flex items-center gap-1">
            <SlidersHorizontal class="w-3 h-3" /> Stack:
          </span>
          <button 
            v-for="presetKey in (['fintech', 'ecommerce', 'logistics'] as const)" 
            :key="presetKey"
            @click="selectedIndustry = presetKey"
            :class="[
              'px-2.5 py-1 rounded text-xs font-mono font-medium transition-all cursor-pointer',
              selectedIndustry === presetKey 
                ? 'bg-white dark:bg-zinc-800 text-emerald-600 dark:text-emerald-400 border border-zinc-200 dark:border-zinc-700 shadow-sm' 
                : 'text-zinc-600 dark:text-zinc-400 hover:text-zinc-900 dark:hover:text-zinc-200'
            ]"
          >
            {{ industryPresets[presetKey].name }}
          </button>
        </div>

        <!-- Time Range Selector (With REAL Interactive Value Binding) -->
        <div class="bg-zinc-100 dark:bg-zinc-900 border border-zinc-200 dark:border-zinc-800 rounded-lg p-1 flex items-center gap-1">
          <button 
            v-for="range in (['24h', '7d', '30d'] as const)" 
            :key="range"
            @click="timeRange = range"
            :class="[
              'px-2.5 py-1 rounded text-xs font-mono transition-all cursor-pointer font-medium',
              timeRange === range 
                ? 'bg-white dark:bg-zinc-800 text-zinc-900 dark:text-zinc-100 shadow-sm border border-zinc-200 dark:border-zinc-700' 
                : 'text-zinc-500 dark:text-zinc-400 hover:text-zinc-900 dark:hover:text-zinc-200'
            ]"
          >
            {{ range }}
          </button>
        </div>

        <!-- Refresh Button -->
        <button 
          @click="handleRefresh"
          :disabled="isRefreshing"
          class="p-2 rounded-lg bg-zinc-100 dark:bg-zinc-900 border border-zinc-200 dark:border-zinc-800 text-zinc-500 dark:text-zinc-400 hover:text-zinc-900 dark:hover:text-zinc-200 hover:bg-zinc-200 dark:hover:bg-zinc-800 transition-colors cursor-pointer"
          title="Refresh Data"
        >
          <RefreshCw :class="['w-3.5 h-3.5', isRefreshing ? 'animate-spin text-emerald-500 dark:text-emerald-400' : '']" />
        </button>
      </div>
    </div>

    <!-- Dynamic KPI Cards Grid -->
    <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
      <div 
        v-for="(item, idx) in dynamicMetrics" 
        :key="idx"
        class="bg-white dark:bg-zinc-900/80 backdrop-blur border border-zinc-200 dark:border-zinc-800/80 rounded-xl p-4.5 hover:border-zinc-300 dark:hover:border-zinc-700/80 transition-all hover:shadow-lg shadow-sm group"
      >
        <div class="flex items-center justify-between">
          <span class="text-[11px] font-mono text-zinc-500 dark:text-zinc-400 uppercase tracking-wider">{{ item.title }}</span>
          <div class="w-7 h-7 rounded-lg bg-zinc-100 dark:bg-zinc-800/80 flex items-center justify-center text-zinc-600 dark:text-zinc-300 group-hover:scale-110 transition-transform">
            <component :is="item.icon" class="w-3.5 h-3.5" />
          </div>
        </div>

        <div class="mt-3 flex items-baseline justify-between">
          <span class="text-xl font-bold tracking-tight text-zinc-900 dark:text-zinc-100 font-mono">{{ item.value }}</span>
          <span class="flex items-center text-xs font-mono font-semibold text-emerald-600 dark:text-emerald-400">
            <ArrowUpRight class="w-3.5 h-3.5 mr-0.5" />
            {{ item.change }}
          </span>
        </div>

        <div class="mt-1.5 text-[11px] text-zinc-400 dark:text-zinc-500 font-mono">
          {{ item.subtext }}
        </div>
      </div>
    </div>

    <!-- Chart & Infrastructure Stack -->
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
      <!-- SVG Vector Chart with Smooth Transition -->
      <div class="lg:col-span-2 bg-white dark:bg-zinc-900/80 backdrop-blur border border-zinc-200 dark:border-zinc-800/80 rounded-xl p-5 shadow-sm">
        <div class="flex items-center justify-between mb-3">
          <div>
            <h3 class="text-xs font-mono font-semibold text-zinc-900 dark:text-zinc-200 flex items-center gap-2">
              <Layers class="w-3.5 h-3.5 text-cyan-500 dark:text-cyan-400" />
              Ingestion & Latency Spectrum (Range: {{ timeRange }})
            </h3>
            <p class="text-[11px] font-mono text-zinc-500 dark:text-zinc-400 mt-0.5">Preset: {{ currentPreset.name }}</p>
          </div>
          <div class="flex items-center gap-4 text-[11px] font-mono">
            <span class="flex items-center gap-1.5 text-zinc-600 dark:text-zinc-400">
              <span class="w-2 h-2 rounded-full bg-cyan-500 dark:bg-cyan-400"></span> Ingestion
            </span>
            <span class="flex items-center gap-1.5 text-zinc-600 dark:text-zinc-400">
              <span class="w-2 h-2 rounded-full bg-violet-500 dark:bg-violet-400"></span> Latency
            </span>
          </div>
        </div>

        <div class="relative h-48 w-full pt-2">
          <svg class="w-full h-full overflow-visible" viewBox="0 0 500 180" preserveAspectRatio="none">
            <defs>
              <linearGradient id="cyanGrad" x1="0" y1="0" x2="0" y2="1">
                <stop offset="0%" stop-color="#22d3ee" stop-opacity="0.25" />
                <stop offset="100%" stop-color="#22d3ee" stop-opacity="0.0" />
              </linearGradient>
            </defs>

            <!-- Grid Lines -->
            <line x1="0" y1="30" x2="500" y2="30" class="stroke-zinc-200 dark:stroke-zinc-800" stroke-dasharray="3" />
            <line x1="0" y1="75" x2="500" y2="75" class="stroke-zinc-200 dark:stroke-zinc-800" stroke-dasharray="3" />
            <line x1="0" y1="120" x2="500" y2="120" class="stroke-zinc-200 dark:stroke-zinc-800" stroke-dasharray="3" />
            <line x1="0" y1="165" x2="500" y2="165" class="stroke-zinc-200 dark:stroke-zinc-800" stroke-dasharray="3" />

            <!-- Dynamic Path Ingestion -->
            <path 
              :d="dynamicCurve.throughput + ' L500,175 L0,175 Z'" 
              fill="url(#cyanGrad)" 
              class="transition-all duration-500 ease-out"
            />
            <path 
              :d="dynamicCurve.throughput" 
              fill="none" 
              stroke="#06b6d4" 
              stroke-width="2" 
              class="transition-all duration-500 ease-out"
            />

            <!-- Dynamic Path Latency -->
            <path 
              :d="dynamicCurve.latency" 
              fill="none" 
              stroke="#8b5cf6" 
              stroke-width="1.8" 
              stroke-dasharray="2" 
              class="transition-all duration-500 ease-out"
            />
          </svg>

          <!-- Dynamic Time Axis Labels -->
          <div class="flex justify-between text-[10px] font-mono text-zinc-400 dark:text-zinc-500 mt-2">
            <span v-for="(lbl, lIdx) in currentTimeMultiplier.timeLabels" :key="lIdx">{{ lbl }}</span>
          </div>
        </div>
      </div>

      <!-- Infrastructure Status Component -->
      <div class="bg-white dark:bg-zinc-900/80 backdrop-blur border border-zinc-200 dark:border-zinc-800/80 rounded-xl p-5 flex flex-col justify-between shadow-sm">
        <div>
          <h3 class="text-xs font-mono font-semibold text-zinc-900 dark:text-zinc-200 flex items-center gap-2 mb-3">
            <ShieldCheck class="w-3.5 h-3.5 text-emerald-600 dark:text-emerald-400" />
            Topology Verification
          </h3>

          <div class="space-y-2.5 text-xs font-mono">
            <div class="flex items-center justify-between p-2 rounded bg-zinc-50 dark:bg-zinc-950/60 border border-zinc-200 dark:border-zinc-800/80">
              <div class="flex items-center gap-2">
                <span class="w-1.5 h-1.5 rounded-full bg-emerald-500 dark:bg-emerald-400"></span>
                <span class="text-zinc-700 dark:text-zinc-300">Auth & Security</span>
              </div>
              <span class="text-[11px] text-zinc-500 dark:text-zinc-400">JWT + OAuth 2.0</span>
            </div>

            <div class="flex items-center justify-between p-2 rounded bg-zinc-50 dark:bg-zinc-950/60 border border-zinc-200 dark:border-zinc-800/80">
              <div class="flex items-center gap-2">
                <span class="w-1.5 h-1.5 rounded-full bg-emerald-500 dark:bg-emerald-400"></span>
                <span class="text-zinc-700 dark:text-zinc-300">Database Engine</span>
              </div>
              <span class="text-[11px] text-zinc-500 dark:text-zinc-400">PostgreSQL / Supabase</span>
            </div>

            <div class="flex items-center justify-between p-2 rounded bg-zinc-50 dark:bg-zinc-950/60 border border-zinc-200 dark:border-zinc-800/80">
              <div class="flex items-center gap-2">
                <span class="w-1.5 h-1.5 rounded-full bg-emerald-500 dark:bg-emerald-400"></span>
                <span class="text-zinc-700 dark:text-zinc-300">Edge Caching</span>
              </div>
              <span class="text-[11px] text-zinc-500 dark:text-zinc-400">Cloudflare Workers</span>
            </div>

            <div class="flex items-center justify-between p-2 rounded bg-zinc-50 dark:bg-zinc-950/60 border border-zinc-200 dark:border-zinc-800/80">
              <div class="flex items-center gap-2">
                <span class="w-1.5 h-1.5 rounded-full bg-emerald-500 dark:bg-emerald-400"></span>
                <span class="text-zinc-700 dark:text-zinc-300">LLM Fallback</span>
              </div>
              <span class="text-[11px] text-zinc-500 dark:text-zinc-400">Claude 3.5 / OpenAI</span>
            </div>
          </div>
        </div>

        <div class="mt-4 pt-3 border-t border-zinc-200 dark:border-zinc-800/80 text-[11px] font-mono text-zinc-500 flex items-center justify-between">
          <span>Target Delivery</span>
          <span class="text-zinc-800 dark:text-zinc-200 font-semibold">3 to 7 Days MVP</span>
        </div>
      </div>
    </div>

    <!-- Live Table -->
    <div class="bg-white dark:bg-zinc-900/80 backdrop-blur border border-zinc-200 dark:border-zinc-800/80 rounded-xl overflow-hidden shadow-sm">
      <div class="p-4 border-b border-zinc-200 dark:border-zinc-800/80 flex items-center justify-between">
        <div class="flex items-center gap-2">
          <Sparkles class="w-3.5 h-3.5 text-amber-500 dark:text-amber-400" />
          <h3 class="text-xs font-mono font-semibold text-zinc-900 dark:text-zinc-200">
            Live Execution Stream ({{ currentPreset.name }})
          </h3>
        </div>
        <span class="text-[10px] font-mono text-zinc-500 dark:text-zinc-400 bg-zinc-100 dark:bg-zinc-800/80 px-2 py-0.5 rounded border border-zinc-200 dark:border-zinc-700/60">
          Sync Rate: 1.4k events/min
        </span>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full text-left text-xs">
          <thead class="bg-zinc-50 dark:bg-zinc-950/60 text-zinc-500 dark:text-zinc-400 font-mono border-b border-zinc-200 dark:border-zinc-800 text-[11px]">
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
          <tbody class="divide-y divide-zinc-200 dark:divide-zinc-800/60 font-mono text-[11px]">
            <tr v-for="evt in currentPreset.activities" :key="evt.id" class="hover:bg-zinc-50 dark:hover:bg-zinc-800/40 transition-colors">
              <td class="px-4 py-2.5 text-zinc-400 dark:text-zinc-500">{{ evt.id }}</td>
              <td class="px-4 py-2.5 font-medium text-cyan-600 dark:text-cyan-400">{{ evt.endpoint }}</td>
              <td class="px-4 py-2.5 text-zinc-700 dark:text-zinc-300">{{ evt.tenant }}</td>
              <td class="px-4 py-2.5 text-zinc-500 dark:text-zinc-400">{{ evt.model }}</td>
              <td class="px-4 py-2.5 text-zinc-700 dark:text-zinc-300">{{ evt.latency }}</td>
              <td class="px-4 py-2.5">
                <span 
                  :class="[
                    'inline-flex items-center gap-1 px-2 py-0.5 rounded-full text-[10px]',
                    evt.status === 'Completed' 
                      ? 'bg-emerald-500/10 text-emerald-600 dark:text-emerald-400 border border-emerald-500/20' 
                      : 'bg-amber-500/10 text-amber-600 dark:text-amber-400 border border-amber-500/20'
                  ]"
                >
                  <CheckCircle2 v-if="evt.status === 'Completed'" class="w-2.5 h-2.5" />
                  <Clock v-else class="w-2.5 h-2.5 animate-spin" />
                  {{ evt.status }}
                </span>
              </td>
              <td class="px-4 py-2.5 text-right text-zinc-400 dark:text-zinc-500">{{ evt.time }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>
