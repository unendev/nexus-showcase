<script setup lang="ts">
import { ref } from 'vue'
import DashboardView from './components/DashboardView.vue'
import WorkflowView from './components/WorkflowView.vue'
import { 
  LayoutDashboard, 
  Workflow, 
  ShieldCheck, 
  Cpu
} from 'lucide-vue-next'

const activeTab = ref<'dashboard' | 'workflow'>('dashboard')
</script>

<template>
  <div class="min-h-screen bg-[#09090b] text-zinc-100 flex flex-col font-sans selection:bg-emerald-500/30 selection:text-emerald-200">
    <!-- Top Global Glass Navbar -->
    <header class="sticky top-0 z-50 bg-zinc-950/80 backdrop-blur-md border-b border-zinc-800/80 px-4 sm:px-8 py-3 flex items-center justify-between">
      <div class="flex items-center gap-3">
        <div class="w-8 h-8 rounded-lg bg-emerald-500/10 border border-emerald-500/30 flex items-center justify-center text-emerald-400">
          <Cpu class="w-4 h-4" />
        </div>
        <div>
          <div class="flex items-center gap-2">
            <h1 class="font-bold text-sm sm:text-base tracking-tight text-zinc-100 font-mono">NexusFlow AI</h1>
            <span class="text-[10px] uppercase font-mono px-2 py-0.5 rounded bg-zinc-800 text-zinc-400 border border-zinc-700/60">
              Live Showcase v2.4
            </span>
          </div>
          <p class="text-[11px] text-zinc-500 hidden sm:block">Full-Stack SaaS MVP & n8n Enterprise Agent Architecture</p>
        </div>
      </div>

      <!-- Center Segmented View Switcher -->
      <div class="bg-zinc-900 border border-zinc-800 rounded-xl p-1 flex items-center gap-1 shadow-inner">
        <button
          @click="activeTab = 'dashboard'"
          :class="[
            'flex items-center gap-2 px-3 sm:px-4 py-1.5 rounded-lg text-xs font-medium transition-all cursor-pointer',
            activeTab === 'dashboard'
              ? 'bg-zinc-800 text-zinc-100 shadow-sm border border-zinc-700/50'
              : 'text-zinc-400 hover:text-zinc-200'
          ]"
        >
          <LayoutDashboard class="w-3.5 h-3.5" />
          <span>SaaS MVP Dashboard</span>
        </button>

        <button
          @click="activeTab = 'workflow'"
          :class="[
            'flex items-center gap-2 px-3 sm:px-4 py-1.5 rounded-lg text-xs font-medium transition-all cursor-pointer',
            activeTab === 'workflow'
              ? 'bg-zinc-800 text-zinc-100 shadow-sm border border-zinc-700/50'
              : 'text-zinc-400 hover:text-zinc-200'
          ]"
        >
          <Workflow class="w-3.5 h-3.5" />
          <span>AI Agent Workflow DAG</span>
        </button>
      </div>

      <!-- Right Authority & Verified Badge -->
      <div class="hidden md:flex items-center gap-3">
        <div class="flex items-center gap-1.5 text-xs font-mono text-zinc-400 bg-zinc-900/60 border border-zinc-800 px-3 py-1 rounded-lg">
          <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span>
          <span>Verified Engineer: Weijun Chen</span>
        </div>
      </div>
    </header>

    <!-- Main Container -->
    <main class="flex-1 max-w-7xl w-full mx-auto p-4 sm:p-8">
      <!-- Transition wrapper between views -->
      <transition mode="out-in" enter-active-class="transition duration-200 ease-out" enter-from-class="opacity-0 translate-y-2" enter-to-class="opacity-100 translate-y-0" leave-active-class="transition duration-150 ease-in" leave-from-class="opacity-100" leave-to-class="opacity-0">
        <component :is="activeTab === 'dashboard' ? DashboardView : WorkflowView" />
      </transition>
    </main>

    <!-- Global Footer with Trust Anchors -->
    <footer class="border-t border-zinc-800/80 bg-zinc-950/40 py-6 px-4 sm:px-8 mt-12 text-xs text-zinc-500">
      <div class="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-4">
        <div class="flex items-center gap-2 font-mono">
          <ShieldCheck class="w-4 h-4 text-emerald-400" />
          <span>Built for Production • Turnkey Delivery • Upwork Verified Contract Ready</span>
        </div>

        <div class="flex items-center gap-6 font-mono text-zinc-400">
          <span>Stack: Vue 3 + TypeScript + Tailwind + DAG Engine</span>
          <span>Zero External DB Dependency (100% SLA)</span>
        </div>
      </div>
    </footer>
  </div>
</template>
