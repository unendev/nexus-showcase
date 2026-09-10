<script setup lang="ts">
import { ref } from 'vue'
import DashboardView from './components/DashboardView.vue'
import WorkflowView from './components/WorkflowView.vue'
import { 
  LayoutDashboard, 
  Workflow, 
  ShieldCheck, 
  Cpu,
  ExternalLink
} from 'lucide-vue-next'

const activeTab = ref<'dashboard' | 'workflow'>('dashboard')
</script>

<template>
  <div class="min-h-screen bg-[#09090b] text-zinc-100 flex flex-col font-sans selection:bg-emerald-500/30 selection:text-emerald-200">
    <!-- Top Global Glass Navbar (Restrained, High-End Silicon Valley Style) -->
    <header class="sticky top-0 z-50 bg-zinc-950/80 backdrop-blur-md border-b border-zinc-800/80 px-4 sm:px-8 py-2.5 flex items-center justify-between">
      <div class="flex items-center gap-3">
        <div class="w-7 h-7 rounded-lg bg-zinc-900 border border-zinc-700/80 flex items-center justify-center text-emerald-400">
          <Cpu class="w-3.5 h-3.5" />
        </div>
        <div class="flex items-center gap-2">
          <span class="font-bold text-sm tracking-tight text-zinc-100 font-mono">NexusFlow</span>
          <span class="w-1.5 h-1.5 rounded-full bg-emerald-400"></span>
          <span class="text-[11px] font-mono text-zinc-500">production</span>
        </div>
      </div>

      <!-- Center Segmented View Switcher -->
      <div class="bg-zinc-900 border border-zinc-800 rounded-lg p-1 flex items-center gap-1 shadow-inner">
        <button
          @click="activeTab = 'dashboard'"
          :class="[
            'flex items-center gap-1.5 px-3 py-1 rounded text-xs font-mono font-medium transition-all cursor-pointer',
            activeTab === 'dashboard'
              ? 'bg-zinc-800 text-zinc-100 shadow-sm border border-zinc-700/60'
              : 'text-zinc-400 hover:text-zinc-200'
          ]"
        >
          <LayoutDashboard class="w-3.5 h-3.5" />
          <span>SaaS Metrics</span>
        </button>

        <button
          @click="activeTab = 'workflow'"
          :class="[
            'flex items-center gap-1.5 px-3 py-1 rounded text-xs font-mono font-medium transition-all cursor-pointer',
            activeTab === 'workflow'
              ? 'bg-zinc-800 text-zinc-100 shadow-sm border border-zinc-700/60'
              : 'text-zinc-400 hover:text-zinc-200'
          ]"
        >
          <Workflow class="w-3.5 h-3.5" />
          <span>Agent DAG Flow</span>
        </button>
      </div>

      <!-- Right Profile & Upwork Handshake -->
      <div class="flex items-center gap-3">
        <div class="flex items-center gap-2 text-xs font-mono text-zinc-400">
          <div class="w-6 h-6 rounded-full bg-zinc-800 border border-zinc-700 flex items-center justify-center text-[10px] text-zinc-200 font-bold">
            WC
          </div>
          <span class="hidden sm:inline text-zinc-300">Weijun Chen</span>
        </div>

        <a 
          href="https://www.upwork.com/freelancers/~018d96e5be7d478cf3" 
          target="_blank" 
          rel="noopener noreferrer"
          class="flex items-center gap-1 px-2.5 py-1 rounded-md bg-emerald-500/10 hover:bg-emerald-500/20 text-emerald-400 border border-emerald-500/30 text-xs font-mono transition-colors cursor-pointer"
        >
          <span>Hire on Upwork</span>
          <ExternalLink class="w-3 h-3" />
        </a>
      </div>
    </header>

    <!-- Main Container -->
    <main class="flex-1 max-w-7xl w-full mx-auto p-4 sm:p-6 lg:p-8">
      <transition mode="out-in" enter-active-class="transition duration-200 ease-out" enter-from-class="opacity-0 translate-y-2" enter-to-class="opacity-100 translate-y-0" leave-active-class="transition duration-150 ease-in" leave-from-class="opacity-100" leave-to-class="opacity-0">
        <component :is="activeTab === 'dashboard' ? DashboardView : WorkflowView" />
      </transition>
    </main>

    <!-- Global Footer with Restrained Engineering Footprint -->
    <footer class="border-t border-zinc-800/80 bg-zinc-950/40 py-5 px-4 sm:px-8 mt-12 text-xs text-zinc-500 font-mono">
      <div class="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-4">
        <div class="flex items-center gap-2">
          <ShieldCheck class="w-3.5 h-3.5 text-emerald-400" />
          <span>Architected & Maintained by Weijun Chen • Available for Contract & Retainer</span>
        </div>

        <div class="flex items-center gap-6 text-zinc-400">
          <span>Edge Delivery: &lt; 300ms SLA</span>
          <span>Zero External Database Coupling</span>
        </div>
      </div>
    </footer>
  </div>
</template>
