<script setup lang="ts">
import { ref } from 'vue'
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
  ChevronRight
} from 'lucide-vue-next'

const isExecuting = ref(false)
const activeStep = ref(-1)
const copied = ref(false)

const steps = ref([
  {
    id: 'step-1',
    name: 'Inbound Webhook Trigger',
    type: 'Ingestion Layer',
    icon: Webhook,
    color: 'emerald',
    status: 'idle', // 'idle' | 'running' | 'done'
    latency: '14ms',
    desc: 'Receives payload from Stripe / Typeform / Custom Webhook.'
  },
  {
    id: 'step-2',
    name: 'Claude 3.5 Intent Extraction',
    type: 'AI Routing Node',
    icon: BrainCircuit,
    color: 'cyan',
    status: 'idle',
    latency: '342ms',
    desc: 'Parses unstructured inquiry & calculates urgency score (0-100).'
  },
  {
    id: 'step-3',
    name: 'High-Priority Filter Gate',
    type: 'Conditional Logic',
    icon: Filter,
    color: 'amber',
    status: 'idle',
    latency: '8ms',
    desc: 'Routes high-value enterprise leads vs automated FAQ responses.'
  },
  {
    id: 'step-4',
    name: 'Multi-Channel Dispatch',
    type: 'Output & Sync',
    icon: Database,
    color: 'violet',
    status: 'idle',
    latency: '52ms',
    desc: 'Upserts to Postgres Vector DB + posts alert to private Slack channel.'
  }
])

const samplePayload = ref(`{
  "event": "lead.qualified",
  "workflow_id": "wf_enterprise_ai_v2",
  "execution_time_ms": 416,
  "status": "success",
  "data": {
    "lead": {
      "company": "Horizon Cloud Technologies",
      "budget_tier": "$10k - $25k",
      "urgency_score": 94,
      "intent": "Full-Stack SaaS MVP with Anthropic API"
    },
    "classification": {
      "priority": "P0_CRITICAL",
      "auto_assigned_engineer": "Weijun Chen (Lead)",
      "route": "vip_slack_dispatch"
    },
    "actions_taken": [
      "crm_upserted_id_7842",
      "slack_alert_channel_enterprise_leads",
      "calendar_invite_drafted"
    ]
  }
}`)

const runWorkflowTest = () => {
  if (isExecuting.value) return
  isExecuting.value = true
  activeStep.value = 0

  steps.value.forEach(s => s.status = 'idle')

  // Step 1
  steps.value[0].status = 'running'
  setTimeout(() => {
    steps.value[0].status = 'done'
    activeStep.value = 1
    steps.value[1].status = 'running'

    // Step 2
    setTimeout(() => {
      steps.value[1].status = 'done'
      activeStep.value = 2
      steps.value[2].status = 'running'

      // Step 3
      setTimeout(() => {
        steps.value[2].status = 'done'
        activeStep.value = 3
        steps.value[3].status = 'running'

        // Step 4
        setTimeout(() => {
          steps.value[3].status = 'done'
          activeStep.value = 4
          isExecuting.value = false
        }, 350)
      }, 250)
    }, 450)
  }, 300)
}

const copyJson = () => {
  navigator.clipboard.writeText(samplePayload.value)
  copied.value = true
  setTimeout(() => copied.value = false, 2000)
}
</script>

<template>
  <div class="space-y-6">
    <!-- View Header & Action Bar -->
    <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-4 pb-2 border-b border-zinc-800/80">
      <div>
        <h2 class="text-xl font-bold text-zinc-100 flex items-center gap-2">
          <span>Enterprise AI Agent & Workflow Orchestrator</span>
          <span class="text-xs px-2 py-0.5 rounded-full bg-cyan-500/10 text-cyan-400 border border-cyan-500/20 font-mono">
            n8n & Custom Node Compatible
          </span>
        </h2>
        <p class="text-sm text-zinc-400 mt-0.5">
          Visual DAG execution pipeline connecting Webhooks, LLM reasoning, and database outputs.
        </p>
      </div>

      <div class="flex items-center gap-3">
        <button
          @click="runWorkflowTest"
          :disabled="isExecuting"
          class="flex items-center gap-2 px-4 py-2 rounded-lg bg-emerald-500 hover:bg-emerald-600 active:bg-emerald-700 text-zinc-950 font-semibold text-xs shadow-lg shadow-emerald-500/20 transition-all cursor-pointer disabled:opacity-50 disabled:cursor-not-allowed"
        >
          <RotateCw v-if="isExecuting" class="w-3.5 h-3.5 animate-spin" />
          <Play v-else class="w-3.5 h-3.5 fill-current" />
          <span>{{ isExecuting ? 'Executing Pipeline...' : '▶ Run Test Workflow' }}</span>
        </button>
      </div>
    </div>

    <!-- Workflow Execution Canvas -->
    <div class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-6 relative overflow-hidden">
      <!-- Background subtle grid pattern -->
      <div class="absolute inset-0 bg-[linear-gradient(to_right,#27272a_1px,transparent_1px),linear-gradient(to_bottom,#27272a_1px,transparent_1px)] bg-[size:24px_24px] opacity-20 pointer-events-none"></div>

      <div class="relative z-10">
        <div class="flex items-center justify-between mb-6">
          <div class="flex items-center gap-2 text-xs font-mono text-zinc-400">
            <span class="w-2 h-2 rounded-full bg-emerald-400"></span>
            <span>Pipeline Status: <strong class="text-zinc-200">{{ isExecuting ? 'Running' : 'Ready' }}</strong></span>
            <span class="text-zinc-600">|</span>
            <span>Total Latency: <strong class="text-zinc-200">416ms</strong></span>
          </div>
          <span class="text-xs font-mono text-zinc-500">Autonomous DAG Engine</span>
        </div>

        <!-- Flow Nodes Chain (Responsive Grid / Flex) -->
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
                : 'border-zinc-800 hover:border-zinc-700'
            ]"
          >
            <!-- Step badge & Icon -->
            <div class="flex items-center justify-between mb-3">
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
                <span class="text-[11px] font-mono text-zinc-500">Node 0{{ idx + 1 }}</span>
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
            <h4 class="text-xs font-semibold text-zinc-200">{{ step.name }}</h4>
            <div class="text-[11px] font-mono text-zinc-400 mt-0.5">{{ step.type }}</div>
            <p class="text-[11px] text-zinc-500 mt-2 line-clamp-2 leading-relaxed">
              {{ step.desc }}
            </p>

            <!-- Connector arrow for desktop (except last item) -->
            <div 
              v-if="idx < steps.length - 1" 
              class="hidden md:flex absolute -right-3 top-1/2 -translate-y-1/2 z-20 w-6 h-6 rounded-full bg-zinc-800 border border-zinc-700 items-center justify-center text-zinc-400"
            >
              <ChevronRight class="w-3.5 h-3.5" />
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Live Execution JSON Inspector & Agent Architecture Specs -->
    <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
      <!-- Left 2 Cols: JSON Debug Console -->
      <div class="lg:col-span-2 bg-zinc-950 border border-zinc-800/80 rounded-xl overflow-hidden font-mono">
        <div class="p-4 bg-zinc-900/80 border-b border-zinc-800 flex items-center justify-between">
          <div class="flex items-center gap-2">
            <Terminal class="w-4 h-4 text-emerald-400" />
            <span class="text-xs font-semibold text-zinc-200">Execution Output Terminal (Structured JSON)</span>
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

        <div class="p-5 text-xs overflow-x-auto text-zinc-300 leading-relaxed max-h-72">
          <pre><code class="text-emerald-400/90">{{ samplePayload }}</code></pre>
        </div>
      </div>

      <!-- Right 1 Col: Production Capabilities Checklist -->
      <div class="bg-zinc-900/80 backdrop-blur border border-zinc-800/80 rounded-xl p-5 flex flex-col justify-between">
        <div>
          <h3 class="text-xs font-semibold text-zinc-200 uppercase tracking-wider mb-4 flex items-center gap-2">
            <Sparkles class="w-3.5 h-3.5 text-cyan-400" />
            Enterprise Workflow Features
          </h3>

          <ul class="space-y-3 text-xs text-zinc-400">
            <li class="flex items-start gap-2">
              <CheckCircle2 class="w-4 h-4 text-emerald-400 shrink-0 mt-0.5" />
              <span><strong>Idempotency & Retry Logic:</strong> Automatic exponential backoff for flaky external APIs.</span>
            </li>
            <li class="flex items-start gap-2">
              <CheckCircle2 class="w-4 h-4 text-emerald-400 shrink-0 mt-0.5" />
              <span><strong>Multi-Model Fallback:</strong> Seamless fallback from Claude 3.5 to GPT-4o on rate limits.</span>
            </li>
            <li class="flex items-start gap-2">
              <CheckCircle2 class="w-4 h-4 text-emerald-400 shrink-0 mt-0.5" />
              <span><strong>Self-Hosted or Cloud:</strong> Ready for n8n Community, Docker, or Railway microservices.</span>
            </li>
          </ul>
        </div>

        <div class="mt-6 pt-4 border-t border-zinc-800 text-xs text-zinc-500">
          <span>Supported Integrations: </span>
          <span class="text-zinc-300 font-mono">HubSpot, Slack, Airtable, PostgreSQL, Supabase, Stripe</span>
        </div>
      </div>
    </div>
  </div>
</template>
