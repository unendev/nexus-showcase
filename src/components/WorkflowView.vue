<script setup lang="ts">
import { ref, computed } from 'vue'
import { 
  Play, 
  CheckCircle2, 
  Clock, 
  Terminal, 
  Webhook, 
  BrainCircuit, 
  Filter, 
  Database, 
  Copy, 
  Check, 
  RotateCw,
  Sparkles,
  ChevronRight,
  SendHorizontal
} from 'lucide-vue-next'

const isExecuting = ref(false)
const copied = ref(false)

// Interactive User Input State
const userPrompt = ref('Urgent enterprise lead from London: Requesting full-stack Next.js SaaS MVP with Stripe payment integration within 5 days, budget $8,000 USD.')

const presetPrompts = [
  { label: 'High-Value Lead', text: 'Urgent enterprise lead from London: Requesting full-stack Next.js SaaS MVP with Stripe payment integration within 5 days, budget $8,000 USD.' },
  { label: 'Stripe Dispute', text: 'Webhook alert: Charge dispute opened on customer ch_91024. Evidence required within 48 hours for visa chargeback.' },
  { label: 'Feature Ingestion', text: 'Feedback parsed: User wants real-time Webhook export and Slack notifications when task status updates to completed.' }
]

const steps = ref([
  {
    id: 'step-1',
    name: 'Inbound Webhook Trigger',
    type: 'Ingestion Layer',
    icon: Webhook,
    status: 'idle', // 'idle' | 'running' | 'done'
    latency: '14ms',
    desc: 'Receives payload from Stripe / Typeform / Custom Webhook.'
  },
  {
    id: 'step-2',
    name: 'Claude 3.5 Intent Extraction',
    type: 'AI Routing Node',
    icon: BrainCircuit,
    status: 'idle',
    latency: '342ms',
    desc: 'Parses unstructured inquiry & calculates urgency score (0-100).'
  },
  {
    id: 'step-3',
    name: 'Priority Filter Gate',
    type: 'Conditional Logic',
    icon: Filter,
    status: 'idle',
    latency: '8ms',
    desc: 'Routes high-value enterprise leads vs automated FAQ responses.'
  },
  {
    id: 'step-4',
    name: 'Multi-Channel Dispatch',
    type: 'Output & Sync',
    icon: Database,
    status: 'idle',
    latency: '52ms',
    desc: 'Upserts to Postgres Vector DB + posts alert to private Slack channel.'
  }
])

const dynamicPayload = computed(() => {
  const isUrgent = userPrompt.value.toLowerCase().includes('urgent') || userPrompt.value.toLowerCase().includes('dispute') || userPrompt.value.toLowerCase().includes('enterprise')
  const score = isUrgent ? 96 : 74
  const tier = userPrompt.value.toLowerCase().includes('dispute') ? 'RISK_MITIGATION' : isUrgent ? 'P0_ENTERPRISE' : 'STANDARD_TIER'

  return JSON.stringify({
    event: "pipeline.executed",
    timestamp_utc: new Date().toISOString(),
    execution_time_ms: 416,
    input_text: userPrompt.value,
    extracted_entities: {
      urgency_score: score,
      classification: tier,
      detected_intent: userPrompt.value.slice(0, 50) + (userPrompt.value.length > 50 ? '...' : ''),
      assigned_engineer: "Weijun Chen (Lead)"
    },
    pipeline_actions: [
      "webhook_signature_verified_256",
      tier === 'P0_ENTERPRISE' ? "slack_vip_channel_alert_sent" : "crm_lead_queued",
      "postgres_vector_record_upserted"
    ]
  }, null, 2)
})

const runWorkflowTest = () => {
  if (isExecuting.value) return
  isExecuting.value = true
  steps.value.forEach(s => s.status = 'idle')

  // Step 1
  steps.value[0].status = 'running'
  setTimeout(() => {
    steps.value[0].status = 'done'
    steps.value[1].status = 'running'

    // Step 2
    setTimeout(() => {
      steps.value[1].status = 'done'
      steps.value[2].status = 'running'

      // Step 3
      setTimeout(() => {
        steps.value[2].status = 'done'
        steps.value[3].status = 'running'

        // Step 4
        setTimeout(() => {
          steps.value[3].status = 'done'
          isExecuting.value = false
        }, 300)
      }, 200)
    }, 400)
  }, 250)
}

const copyJson = () => {
  navigator.clipboard.writeText(dynamicPayload.value)
  copied.value = true
  setTimeout(() => copied.value = false, 2000)
}
</script>

<template>
  <div class="space-y-6">
    <!-- View Header & Action Bar -->
    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 pb-2 border-b border-zinc-800/80">
      <div>
        <div class="flex items-center gap-2">
          <h2 class="text-lg font-bold text-zinc-100 font-mono tracking-tight">AI Agent Workflow DAG</h2>
          <span class="text-xs px-2 py-0.5 rounded bg-zinc-800 text-zinc-300 border border-zinc-700 font-mono">
            n8n / LangGraph Engine
          </span>
        </div>
        <p class="text-xs text-zinc-400 mt-0.5 font-mono">
          Visual DAG execution pipeline connecting Webhooks, LLM reasoning, and database outputs.
        </p>
      </div>

      <div class="flex items-center gap-3">
        <button
          @click="runWorkflowTest"
          :disabled="isExecuting"
          class="flex items-center gap-2 px-4 py-2 rounded-lg bg-emerald-500 hover:bg-emerald-600 active:bg-emerald-700 text-zinc-950 font-mono font-semibold text-xs shadow-lg shadow-emerald-500/20 transition-all cursor-pointer disabled:opacity-50 disabled:cursor-not-allowed"
        >
          <RotateCw v-if="isExecuting" class="w-3.5 h-3.5 animate-spin" />
          <Play v-else class="w-3.5 h-3.5 fill-current" />
          <span>{{ isExecuting ? 'Running DAG Nodes...' : '▶ Execute Pipeline' }}</span>
        </button>
      </div>
    </div>

    <!-- Interactive Custom Input Bar for Client Testing -->
    <div class="bg-zinc-900/90 border border-zinc-800/90 rounded-xl p-4 space-y-3">
      <div class="flex items-center justify-between">
        <label class="text-xs font-mono text-zinc-300 flex items-center gap-2 font-semibold">
          <SendHorizontal class="w-3.5 h-3.5 text-cyan-400" />
          <span>Simulate Client Webhook Payload / Natural Language Trigger:</span>
        </label>
        
        <!-- Quick Preset Badges -->
        <div class="flex items-center gap-1.5">
          <span class="text-[10px] font-mono text-zinc-500 hidden sm:inline">Try preset:</span>
          <button
            v-for="(preset, pIdx) in presetPrompts"
            :key="pIdx"
            @click="userPrompt = preset.text; runWorkflowTest()"
            class="text-[11px] font-mono px-2 py-0.5 rounded bg-zinc-800 hover:bg-zinc-700 text-zinc-300 transition-colors cursor-pointer border border-zinc-700/60"
          >
            {{ preset.label }}
          </button>
        </div>
      </div>

      <div class="flex gap-2">
        <input 
          v-model="userPrompt" 
          type="text" 
          placeholder="Type any custom business event or message..."
          class="flex-1 bg-zinc-950/80 border border-zinc-700/80 rounded-lg px-3 py-2 text-xs font-mono text-zinc-200 focus:outline-none focus:border-cyan-400/80 transition-colors"
          @keydown.enter="runWorkflowTest"
        />
        <button
          @click="runWorkflowTest"
          :disabled="isExecuting"
          class="px-4 py-2 bg-zinc-800 hover:bg-zinc-700 text-zinc-200 border border-zinc-700 rounded-lg text-xs font-mono transition-colors cursor-pointer flex items-center gap-1.5"
        >
          <span>Run</span>
          <ChevronRight class="w-3 h-3" />
        </button>
      </div>
    </div>

    <!-- Workflow Execution Canvas -->
    <div class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-5 relative overflow-hidden">
      <!-- Background subtle grid pattern -->
      <div class="absolute inset-0 bg-[linear-gradient(to_right,#27272a_1px,transparent_1px),linear-gradient(to_bottom,#27272a_1px,transparent_1px)] bg-[size:24px_24px] opacity-20 pointer-events-none"></div>

      <div class="relative z-10">
        <div class="flex items-center justify-between mb-5">
          <div class="flex items-center gap-2 text-xs font-mono text-zinc-400">
            <span class="w-2 h-2 rounded-full bg-emerald-400"></span>
            <span>DAG Status: <strong class="text-zinc-200">{{ isExecuting ? 'Processing' : 'Idle' }}</strong></span>
            <span class="text-zinc-600">|</span>
            <span>SLA: <strong class="text-zinc-200">&lt; 500ms</strong></span>
          </div>
          <span class="text-[11px] font-mono text-zinc-500">Node Parallelism: Enabled</span>
        </div>

        <!-- Flow Nodes Chain -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4 relative">
          <div 
            v-for="(step, idx) in steps" 
            :key="step.id"
            :class="[
              'rounded-xl border p-4 transition-all duration-300 relative bg-zinc-950/80 backdrop-blur',
              step.status === 'running' 
                ? 'border-cyan-400 ring-2 ring-cyan-400/20 shadow-lg shadow-cyan-500/10' 
                : step.status === 'done'
                ? 'border-emerald-500/80 shadow-md shadow-emerald-500/5'
                : 'border-zinc-800/80 hover:border-zinc-700'
            ]"
          >
            <!-- Step badge & Icon -->
            <div class="flex items-center justify-between mb-2.5">
              <div class="flex items-center gap-2">
                <div 
                  :class="[
                    'w-7 h-7 rounded-lg flex items-center justify-center transition-colors',
                    step.status === 'done' ? 'bg-emerald-500/20 text-emerald-400' :
                    step.status === 'running' ? 'bg-cyan-500/20 text-cyan-400' : 'bg-zinc-800 text-zinc-400'
                  ]"
                >
                  <component :is="step.icon" class="w-3.5 h-3.5" />
                </div>
                <span class="text-[10px] font-mono text-zinc-500">Node 0{{ idx + 1 }}</span>
              </div>

              <!-- Status indicator badge -->
              <span 
                :class="[
                  'text-[10px] font-mono px-2 py-0.5 rounded-full flex items-center gap-1',
                  step.status === 'done' ? 'bg-emerald-500/10 text-emerald-400' :
                  step.status === 'running' ? 'bg-cyan-500/10 text-cyan-400 animate-pulse' : 'bg-zinc-800 text-zinc-500'
                ]"
              >
                <CheckCircle2 v-if="step.status === 'done'" class="w-2.5 h-2.5" />
                <Clock v-else-if="step.status === 'running'" class="w-2.5 h-2.5 animate-spin" />
                {{ step.status === 'done' ? step.latency : step.status === 'running' ? 'Active' : 'Standby' }}
              </span>
            </div>

            <!-- Title & Type -->
            <h4 class="text-xs font-semibold text-zinc-200 font-mono">{{ step.name }}</h4>
            <div class="text-[10px] font-mono text-zinc-400 mt-0.5">{{ step.type }}</div>
            <p class="text-[11px] text-zinc-500 mt-1.5 leading-relaxed">
              {{ step.desc }}
            </p>

            <!-- Connector arrow for desktop -->
            <div 
              v-if="idx < steps.length - 1" 
              class="hidden md:flex absolute -right-3 top-1/2 -translate-y-1/2 z-20 w-5 h-5 rounded-full bg-zinc-800 border border-zinc-700 items-center justify-center text-zinc-400"
            >
              <ChevronRight class="w-3 h-3" />
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Live Execution JSON Inspector & Agent Architecture Specs -->
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
      <!-- Left 2 Cols: Dynamic JSON Terminal -->
      <div class="lg:col-span-2 bg-zinc-950 border border-zinc-800/80 rounded-xl overflow-hidden font-mono">
        <div class="p-3.5 bg-zinc-900/80 border-b border-zinc-800/80 flex items-center justify-between">
          <div class="flex items-center gap-2">
            <Terminal class="w-3.5 h-3.5 text-emerald-400" />
            <span class="text-xs font-semibold text-zinc-200">Execution Output (Live Dynamic JSON)</span>
          </div>

          <button 
            @click="copyJson"
            class="flex items-center gap-1 text-[11px] px-2.5 py-1 rounded bg-zinc-800 hover:bg-zinc-700 text-zinc-300 transition-colors cursor-pointer"
          >
            <Check v-if="copied" class="w-3 h-3 text-emerald-400" />
            <Copy v-else class="w-3 h-3" />
            <span>{{ copied ? 'Copied' : 'Copy Payload' }}</span>
          </button>
        </div>

        <div class="p-4 text-xs overflow-x-auto text-zinc-300 leading-relaxed max-h-72">
          <pre><code class="text-emerald-400/90">{{ dynamicPayload }}</code></pre>
        </div>
      </div>

      <!-- Right 1 Col: Production Capabilities Checklist -->
      <div class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-5 flex flex-col justify-between font-mono">
        <div>
          <h3 class="text-xs font-semibold text-zinc-200 uppercase tracking-wider mb-3 flex items-center gap-2">
            <Sparkles class="w-3.5 h-3.5 text-cyan-400" />
            Production Engine Specs
          </h3>

          <ul class="space-y-3 text-xs text-zinc-400">
            <li class="flex items-start gap-2">
              <CheckCircle2 class="w-3.5 h-3.5 text-emerald-400 shrink-0 mt-0.5" />
              <span><strong>Idempotency:</strong> Deduplication and exponential backoff retry policies.</span>
            </li>
            <li class="flex items-start gap-2">
              <CheckCircle2 class="w-3.5 h-3.5 text-emerald-400 shrink-0 mt-0.5" />
              <span><strong>Multi-Model Fallback:</strong> Claude 3.5 Sonnet $\rightarrow$ GPT-4o auto-switch.</span>
            </li>
            <li class="flex items-start gap-2">
              <CheckCircle2 class="w-3.5 h-3.5 text-emerald-400 shrink-0 mt-0.5" />
              <span><strong>Private Deployment:</strong> n8n self-hosted, Docker container or serverless.</span>
            </li>
          </ul>
        </div>

        <div class="mt-4 pt-3 border-t border-zinc-800/80 text-[11px] text-zinc-500">
          <span>Supported Integrations: </span>
          <span class="text-zinc-300">HubSpot, Slack, Airtable, PostgreSQL, Supabase, Stripe</span>
        </div>
      </div>
    </div>
  </div>
</template>
