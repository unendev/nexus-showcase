<script setup lang="ts">
import { ref, onMounted } from 'vue'
import DashboardView from './components/DashboardView.vue'
import WorkflowView from './components/WorkflowView.vue'
import { 
  LayoutDashboard, 
  Workflow, 
  ShieldCheck, 
  Cpu, 
  ExternalLink, 
  Sun, 
  Moon,
  ChevronDown,
  Copy,
  Check
} from 'lucide-vue-next'

const activeTab = ref<'dashboard' | 'workflow'>('dashboard')
const isDark = ref(true)
const isDropdownOpen = ref(false)
const copied = ref(false)

const toggleDropdown = () => {
  isDropdownOpen.value = !isDropdownOpen.value
}

const copyProfileUrl = async () => {
  try {
    await navigator.clipboard.writeText('https://www.upwork.com/freelancers/~01ffb45b4146dbd697')
    copied.value = true
    setTimeout(() => {
      copied.value = false
    }, 2000)
  } catch (e) {
    console.error(e)
  }
}

const toggleTheme = () => {
  isDark.value = !isDark.value
  if (isDark.value) {
    document.documentElement.classList.add('dark')
    localStorage.setItem('theme', 'dark')
  } else {
    document.documentElement.classList.remove('dark')
    localStorage.setItem('theme', 'light')
  }
}

onMounted(() => {
  const saved = localStorage.getItem('theme')
  if (saved === 'light') {
    isDark.value = false
    document.documentElement.classList.remove('dark')
  } else {
    isDark.value = true
    document.documentElement.classList.add('dark')
  }

  const handleClickOutside = (e: MouseEvent) => {
    const target = e.target as HTMLElement
    if (!target.closest('#architect-menu-container')) {
      isDropdownOpen.value = false
    }
  }
  window.addEventListener('click', handleClickOutside)
})
</script>

<template>
  <div class="min-h-screen bg-zinc-50 dark:bg-[#09090b] text-zinc-900 dark:text-zinc-100 flex flex-col font-sans transition-colors duration-200 selection:bg-emerald-500/30 selection:text-emerald-200">
    <!-- Top Global Glass Navbar (Restrained Silicon Valley Engineering Style) -->
    <header class="sticky top-0 z-50 bg-white/80 dark:bg-zinc-950/80 backdrop-blur-md border-b border-zinc-200 dark:border-zinc-800/80 px-4 sm:px-8 py-2.5 flex items-center justify-between shadow-xs">
      <div class="flex items-center gap-3">
        <div class="w-7 h-7 rounded-lg bg-zinc-100 dark:bg-zinc-900 border border-zinc-300 dark:border-zinc-700/80 flex items-center justify-center text-emerald-600 dark:text-emerald-400">
          <Cpu class="w-3.5 h-3.5" />
        </div>
        <div class="flex items-center gap-2">
          <!-- Rebranded to RelayOps -->
          <span class="font-bold text-sm tracking-tight text-zinc-900 dark:text-zinc-100 font-mono">RelayOps</span>
          <span class="w-1.5 h-1.5 rounded-full bg-emerald-500 dark:bg-emerald-400"></span>
          <span class="text-[11px] font-mono text-zinc-400 dark:text-zinc-500">production</span>
        </div>
      </div>

      <!-- Center Segmented View Switcher -->
      <div class="bg-zinc-100 dark:bg-zinc-900 border border-zinc-200 dark:border-zinc-800 rounded-lg p-1 flex items-center gap-1 shadow-inner">
        <button
          @click="activeTab = 'dashboard'"
          :class="[
            'flex items-center gap-1.5 px-3 py-1 rounded text-xs font-mono font-medium transition-all cursor-pointer',
            activeTab === 'dashboard'
              ? 'bg-white dark:bg-zinc-800 text-zinc-900 dark:text-zinc-100 shadow-sm border border-zinc-200 dark:border-zinc-700/60'
              : 'text-zinc-500 dark:text-zinc-400 hover:text-zinc-900 dark:hover:text-zinc-200'
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
              ? 'bg-white dark:bg-zinc-800 text-zinc-900 dark:text-zinc-100 shadow-sm border border-zinc-200 dark:border-zinc-700/60'
              : 'text-zinc-500 dark:text-zinc-400 hover:text-zinc-900 dark:hover:text-zinc-200'
          ]"
        >
          <Workflow class="w-3.5 h-3.5" />
          <span>Agent DAG Flow</span>
        </button>
      </div>

      <!-- Right Profile, Theme Switcher & Upwork Link -->
      <div class="flex items-center gap-2.5">
        <!-- Theme Toggle Button (Light / Dark) -->
        <button
          @click="toggleTheme"
          class="p-1.5 rounded-md bg-zinc-100 dark:bg-zinc-900 border border-zinc-200 dark:border-zinc-800 text-zinc-600 dark:text-zinc-400 hover:text-zinc-900 dark:hover:text-zinc-200 transition-colors cursor-pointer"
          :title="isDark ? 'Switch to Light mode' : 'Switch to Dark mode'"
        >
          <Sun v-if="isDark" class="w-3.5 h-3.5 text-amber-400" />
          <Moon v-else class="w-3.5 h-3.5 text-zinc-600" />
        </button>

        <!-- Architect & System Admin Profile Menu -->
        <div id="architect-menu-container" class="relative">
          <button
            @click.stop="toggleDropdown"
            class="flex items-center gap-2 p-1 sm:px-2.5 sm:py-1 rounded-lg bg-zinc-100 dark:bg-zinc-900 border border-zinc-200 dark:border-zinc-800 text-zinc-700 dark:text-zinc-300 hover:border-zinc-300 dark:hover:border-zinc-700 transition-colors cursor-pointer"
            title="System Architect Profile & Specs"
          >
            <div class="relative w-6 h-6 rounded-full bg-zinc-200 dark:bg-zinc-800 border border-zinc-300 dark:border-zinc-700 flex items-center justify-center text-[10px] text-zinc-800 dark:text-zinc-200 font-bold font-mono">
              WC
              <span class="absolute -bottom-0.5 -right-0.5 w-2 h-2 rounded-full bg-emerald-500 border-2 border-white dark:border-zinc-950"></span>
            </div>
            <div class="hidden sm:flex flex-col text-left font-mono leading-tight">
              <span class="text-xs font-semibold text-zinc-900 dark:text-zinc-100">Weijun Chen</span>
              <span class="text-[10px] text-emerald-600 dark:text-emerald-400">Lead Architect</span>
            </div>
            <ChevronDown class="w-3.5 h-3.5 text-zinc-400 transition-transform duration-200" :class="{ 'rotate-180': isDropdownOpen }" />
          </button>

          <!-- Dropdown Card -->
          <transition
            enter-active-class="transition duration-150 ease-out"
            enter-from-class="opacity-0 scale-95 translate-y-1"
            enter-to-class="opacity-100 scale-100 translate-y-0"
            leave-active-class="transition duration-100 ease-in"
            leave-from-class="opacity-100 scale-100 translate-y-0"
            leave-to-class="opacity-0 scale-95 translate-y-1"
          >
            <div 
              v-if="isDropdownOpen" 
              class="absolute right-0 mt-2 w-72 bg-white dark:bg-zinc-900 border border-zinc-200 dark:border-zinc-800 rounded-xl shadow-2xl p-4 z-50 font-mono"
            >
              <div class="flex items-start justify-between pb-3 border-b border-zinc-100 dark:border-zinc-800">
                <div>
                  <div class="flex items-center gap-1.5">
                    <span class="font-bold text-sm text-zinc-900 dark:text-zinc-100">Weijun Chen</span>
                    <span class="px-1.5 py-0.2 rounded bg-emerald-500/10 text-emerald-600 dark:text-emerald-400 text-[10px] font-semibold">Verified Pro</span>
                  </div>
                  <p class="text-[11px] text-zinc-500 mt-0.5">Lead Full-Stack & Systems Architect</p>
                </div>
              </div>

              <div class="py-3 space-y-2 text-xs border-b border-zinc-100 dark:border-zinc-800">
                <div class="flex items-center justify-between text-zinc-500 dark:text-zinc-400">
                  <span>Production SLA:</span>
                  <span class="text-zinc-800 dark:text-zinc-200 font-semibold">3-7 Days MVP</span>
                </div>
                <div class="flex items-center justify-between text-zinc-500 dark:text-zinc-400">
                  <span>Upwork Status:</span>
                  <span class="text-emerald-600 dark:text-emerald-400 font-medium flex items-center gap-1">
                    <span class="w-1.5 h-1.5 rounded-full bg-emerald-500 animate-ping"></span>
                    Available for Contract
                  </span>
                </div>
                <div class="flex items-center justify-between text-zinc-500 dark:text-zinc-400">
                  <span>Stack Domain:</span>
                  <span class="text-zinc-700 dark:text-zinc-300">Vue · TS · Python · n8n</span>
                </div>
              </div>

              <div class="pt-3 flex flex-col gap-2">
                <a 
                  href="https://www.upwork.com/freelancers/~01ffb45b4146dbd697" 
                  target="_blank" 
                  rel="noopener noreferrer"
                  class="w-full flex items-center justify-center gap-1.5 py-2 px-3 rounded-lg bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-semibold shadow-xs transition-colors"
                >
                  <span>Open Upwork Profile & Hire</span>
                  <ExternalLink class="w-3.5 h-3.5" />
                </a>

                <button 
                  @click="copyProfileUrl"
                  class="w-full flex items-center justify-center gap-1.5 py-1.5 px-3 rounded-lg bg-zinc-100 dark:bg-zinc-800 hover:bg-zinc-200 dark:hover:bg-zinc-700 text-zinc-700 dark:text-zinc-300 text-xs transition-colors cursor-pointer"
                >
                  <component :is="copied ? Check : Copy" class="w-3.5 h-3.5 text-emerald-500" />
                  <span>{{ copied ? 'Link Copied to Clipboard!' : 'Copy Direct Profile URL' }}</span>
                </button>
              </div>
            </div>
          </transition>
        </div>

        <!-- Quick Hire Direct CTA on Navbar -->
        <a 
          href="https://www.upwork.com/freelancers/~01ffb45b4146dbd697" 
          target="_blank" 
          rel="noopener noreferrer"
          class="hidden sm:flex items-center gap-1 px-2.5 py-1 rounded-md bg-emerald-500/10 hover:bg-emerald-500/20 text-emerald-600 dark:text-emerald-400 border border-emerald-500/30 text-xs font-mono transition-colors cursor-pointer font-medium"
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
    <footer class="border-t border-zinc-200 dark:border-zinc-800/80 bg-zinc-100/50 dark:bg-zinc-950/40 py-5 px-4 sm:px-8 mt-12 text-xs text-zinc-500 font-mono">
      <div class="max-w-7xl mx-auto flex flex-col sm:flex-row items-center justify-between gap-4">
        <div class="flex items-center gap-2">
          <ShieldCheck class="w-3.5 h-3.5 text-emerald-600 dark:text-emerald-400" />
          <span>Architected by Weijun Chen • RelayOps Production Spec</span>
        </div>

        <div class="flex items-center gap-6 text-zinc-500 dark:text-zinc-400">
          <span>Edge SLA: &lt; 300ms</span>
          <span>Zero External DB Dependency</span>
        </div>
      </div>
    </footer>
  </div>
</template>
