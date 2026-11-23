<!--
  Vista: Dashboard
  
  Página principal del sistema después del login.
  Muestra tarjetas de bienvenida e información del usuario actual.
  
  NOTA: El header y sidebar son manejados por AppLayout,
  esta vista solo contiene el contenido específico del dashboard.
-->

<template>
  <div class="dashboard-page">
    <section class="welcome-panel">
      <div>
        <span class="eyebrow">Panel principal</span>
        <h1>Buenos días, {{ firstName }}</h1>
        <p>{{ welcomeMessage }}</p>
      </div>
      <div class="date-card">
        <i class="fas fa-calendar-day"></i>
        <div>
          <span>Hoy</span>
          <strong>{{ formattedDate }}</strong>
        </div>
      </div>
    </section>

    <section class="summary-grid" aria-label="Resumen del sistema">
      <article class="summary-card">
        <span class="summary-icon green"><i class="fas fa-file-medical"></i></span>
        <div>
          <span>Casos clínicos</span>
          <strong>Acceso completo</strong>
        </div>
      </article>
      <article class="summary-card">
        <span class="summary-icon blue"><i class="fas fa-user-injured"></i></span>
        <div>
          <span>Pacientes</span>
          <strong>Gestión clínica</strong>
        </div>
      </article>
      <article class="summary-card">
        <span class="summary-icon red"><i class="fas fa-calendar-check"></i></span>
        <div>
          <span>Agenda</span>
          <strong>Visitas y citas</strong>
        </div>
      </article>
      <article class="summary-card">
        <span class="summary-icon gold"><i class="fas fa-folder-open"></i></span>
        <div>
          <span>Archivos</span>
          <strong>Documentación</strong>
        </div>
      </article>
    </section>

    <section class="dashboard-grid">
      <article class="panel quick-panel">
        <div class="panel-heading">
          <div>
            <span class="eyebrow">Accesos directos</span>
            <h2>¿Qué deseas hacer?</h2>
          </div>
        </div>
        <div class="quick-actions">
          <router-link
            v-for="action in quickActions"
            :key="action.to"
            :to="action.to"
            class="quick-action"
          >
            <span class="action-icon"><i :class="action.icon"></i></span>
            <span>
              <strong>{{ action.label }}</strong>
              <small>{{ action.description }}</small>
            </span>
            <i class="fas fa-chevron-right action-arrow"></i>
          </router-link>
        </div>
      </article>

      <article class="panel account-panel">
        <div class="panel-heading">
          <div>
            <span class="eyebrow">Sesión actual</span>
            <h2>Tu cuenta</h2>
          </div>
        </div>
        <div class="account-profile">
          <span class="account-avatar">{{ firstName.charAt(0).toUpperCase() }}</span>
          <div>
            <strong>{{ authStore.fullName }}</strong>
            <span>{{ authStore.user?.email }}</span>
          </div>
        </div>
        <div class="account-status">
          <span class="status-dot"></span>
          <span>Sesión activa</span>
          <strong>{{ authStore.userRole }}</strong>
        </div>
        <router-link to="/perfil" class="profile-link">
          Ver perfil
          <i class="fas fa-arrow-right"></i>
        </router-link>
      </article>
    </section>
  </div>
</template>

<script setup lang="ts">
import { computed } from 'vue'
import { useAuthStore } from '@/stores/auth'
const authStore = useAuthStore()

const firstName = computed(() => authStore.user?.nombre || 'Usuario')
const formattedDate = computed(() =>
  new Intl.DateTimeFormat('es-EC', {
    weekday: 'long',
    day: 'numeric',
    month: 'long'
  }).format(new Date())
)

const welcomeMessage = computed(() => {
  const role = authStore.userRole
  if (role === 'Administrador') return 'Administra usuarios, especialidades y recursos del sistema.'
  if (role === 'Profesor') return 'Revisa casos, realiza observaciones y acompaña a tus estudiantes.'
  if (role === 'Estudiante') return 'Gestiona pacientes, casos clínicos y actividades asignadas.'
  return 'Accede a las herramientas disponibles para tu rol.'
})

const quickActions = computed(() => {
  const role = authStore.userRole
  if (role === 'Administrador') {
    return [
      { to: '/admin/usuarios', icon: 'fas fa-users', label: 'Gestionar usuarios', description: 'Cuentas y roles del sistema' },
      { to: '/admin/especialidades', icon: 'fas fa-stethoscope', label: 'Especialidades', description: 'Catálogo académico clínico' },
      { to: '/profesor/casos-pendientes', icon: 'fas fa-inbox', label: 'Casos clínicos', description: 'Revisar actividad registrada' }
    ]
  }
  if (role === 'Profesor') {
    return [
      { to: '/profesor/casos-pendientes', icon: 'fas fa-inbox', label: 'Revisar casos', description: 'Casos pendientes de aprobación' },
      { to: '/profesor/estudiantes', icon: 'fas fa-user-graduate', label: 'Mis estudiantes', description: 'Seguimiento académico' },
      { to: '/profesor/archivos', icon: 'fas fa-folder-open', label: 'Gestión de archivos', description: 'Documentos de la clínica' }
    ]
  }
  return [
    { to: '/estudiantes/pacientes', icon: 'fas fa-user-injured', label: 'Pacientes', description: 'Consultar y registrar pacientes' },
    { to: '/estudiantes/casos-clinicos/nuevo', icon: 'fas fa-file-medical', label: 'Nuevo caso', description: 'Crear una historia clínica' },
    { to: '/estudiantes/agenda', icon: 'fas fa-calendar-alt', label: 'Agenda', description: 'Revisar citas asignadas' }
  ]
})
</script>

<style scoped>
.dashboard-page {
  max-width: 1380px;
  margin: 0 auto;
}

.welcome-panel {
  min-height: 180px;
  border-radius: 1.25rem;
  padding: 2rem 2.25rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 2rem;
  color: white;
  background:
    linear-gradient(120deg, rgba(18, 67, 43, 0.98), rgba(31, 90, 59, 0.92)),
    var(--gradient-primary);
  box-shadow: 0 14px 32px rgba(31, 90, 59, 0.16);
}

.welcome-panel h1 {
  margin: 0.35rem 0 0.5rem;
  font-size: clamp(1.75rem, 3vw, 2.35rem);
  line-height: 1.12;
}

.welcome-panel p {
  max-width: 620px;
  color: rgba(255, 255, 255, 0.78);
}

.eyebrow {
  display: block;
  margin-bottom: 0.25rem;
  font-size: 0.72rem;
  font-weight: 800;
  text-transform: uppercase;
  letter-spacing: 0.11em;
}

.welcome-panel .eyebrow {
  color: rgba(255, 255, 255, 0.72);
}

.date-card {
  min-width: 210px;
  display: flex;
  align-items: center;
  gap: 0.75rem;
  border: 1px solid rgba(255, 255, 255, 0.16);
  border-radius: 0.85rem;
  padding: 0.85rem 1rem;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(8px);
}

.date-card i {
  width: 38px;
  height: 38px;
  border-radius: 0.65rem;
  display: grid;
  place-items: center;
  background: rgba(255, 255, 255, 0.14);
}

.date-card div {
  display: flex;
  flex-direction: column;
}

.date-card span {
  font-size: 0.72rem;
  color: rgba(255, 255, 255, 0.72);
}

.date-card strong {
  margin-top: 0.15rem;
  font-size: 0.86rem;
  text-transform: capitalize;
}

.summary-grid {
  display: grid;
  grid-template-columns: repeat(4, minmax(0, 1fr));
  gap: 1rem;
  margin: 1.25rem 0;
}

.summary-card,
.panel {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--border-radius-lg);
  box-shadow: var(--shadow-sm);
}

.summary-card {
  min-height: 112px;
  padding: 1.25rem;
  display: flex;
  align-items: center;
  gap: 0.875rem;
}

.summary-card div {
  display: flex;
  flex-direction: column;
  min-width: 0;
}

.summary-card div span {
  color: var(--color-text-secondary);
  font-size: 0.78rem;
}

.summary-card div strong {
  margin-top: 0.25rem;
  color: var(--color-text-primary);
  font-size: 0.95rem;
}

.summary-icon {
  width: 46px;
  height: 46px;
  border-radius: 0.85rem;
  display: grid;
  place-items: center;
  flex-shrink: 0;
}

.summary-icon.green { background: #e8f2ec; color: #2f7652; }
.summary-icon.blue { background: #edf3f7; color: #3e6f8e; }
.summary-icon.red { background: #f7ebec; color: #a84248; }
.summary-icon.gold { background: #f7f1df; color: #9b7026; }

.dashboard-grid {
  display: grid;
  grid-template-columns: minmax(0, 1.65fr) minmax(260px, 0.75fr);
  gap: 1.25rem;
}

.panel {
  padding: 1.35rem;
}

.panel-heading h2 {
  margin: 0;
  font-size: 1.15rem;
  color: var(--color-text-primary);
}

.panel-heading .eyebrow {
  color: var(--color-primary);
}

.quick-actions {
  display: grid;
  gap: 0.65rem;
  margin-top: 1rem;
}

.quick-action {
  min-height: 72px;
  display: grid;
  grid-template-columns: 42px 1fr auto;
  align-items: center;
  gap: 0.75rem;
  color: inherit;
  text-decoration: none;
  border: 1px solid var(--color-border-light);
  border-radius: 0.85rem;
  padding: 0.75rem;
  transition: 0.2s ease;
}

.quick-action:hover {
  border-color: rgba(31, 90, 59, 0.28);
  background: var(--color-background-secondary);
  transform: translateX(3px);
}

.action-icon {
  width: 42px;
  height: 42px;
  border-radius: 0.75rem;
  display: grid;
  place-items: center;
  background: #edf3ef;
  color: var(--color-primary);
}

.quick-action > span:nth-child(2) {
  display: flex;
  flex-direction: column;
}

.quick-action strong {
  color: var(--color-text-primary);
  font-size: 0.9rem;
}

.quick-action small {
  margin-top: 0.15rem;
  color: var(--color-text-muted);
  font-size: 0.75rem;
}

.action-arrow {
  color: var(--color-text-muted);
  font-size: 0.7rem;
}

.account-profile {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin-top: 1.25rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid var(--color-border-light);
}

.account-avatar {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  background: var(--gradient-primary);
  color: white;
  font-size: 1.1rem;
  font-weight: 800;
}

.account-profile div {
  min-width: 0;
  display: flex;
  flex-direction: column;
}

.account-profile strong {
  color: var(--color-text-primary);
}

.account-profile div span {
  margin-top: 0.2rem;
  color: var(--color-text-muted);
  overflow-wrap: anywhere;
  font-size: 0.78rem;
}

.account-status {
  display: grid;
  grid-template-columns: auto 1fr auto;
  align-items: center;
  gap: 0.5rem;
  margin: 1rem 0;
  color: var(--color-text-secondary);
  font-size: 0.78rem;
}

.account-status strong {
  color: var(--color-primary);
  font-size: 0.72rem;
  text-transform: uppercase;
}

.status-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  background: #3aa66a;
  box-shadow: 0 0 0 4px rgba(58, 166, 106, 0.12);
}

.profile-link {
  height: 42px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-radius: 0.75rem;
  padding: 0 0.85rem;
  background: var(--color-background-secondary);
  color: var(--color-primary);
  text-decoration: none;
  font-size: 0.82rem;
  font-weight: 700;
}

@media (max-width: 768px) {
  .dashboard-page {
    margin: 0;
  }

  .welcome-panel {
    align-items: flex-start;
    flex-direction: column;
    padding: 1.25rem;
  }

  .date-card {
    min-width: 0;
    width: 100%;
  }

  .summary-grid {
    grid-template-columns: repeat(2, minmax(0, 1fr));
  }

  .dashboard-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 480px) {
  .summary-grid {
    grid-template-columns: 1fr;
  }

  .summary-card {
    min-height: 86px;
  }
}
</style>