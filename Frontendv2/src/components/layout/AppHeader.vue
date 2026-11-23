<!--
  Componente: AppHeader

  Header reutilizable de la aplicación con:
  - Logo y branding institucional
  - Selector de tema claro/oscuro
  - Información del usuario actual
  - Acceso a perfil
  - Botón de cerrar sesión
-->

<script setup lang="ts">
import { useAuthStore } from '@/stores/auth'
import { useTheme } from '@/composables/useTheme'
import { useRouter } from 'vue-router'

const authStore = useAuthStore()
const { toggleTheme, isDark } = useTheme()
const router = useRouter()

const handleLogout = () => {
  authStore.logout()
  router.push('/login')
}

const handleGoProfile = () => {
  router.push('/perfil')
}
</script>

<template>
  <header class="app-header">
    <div class="header-content">
      <div class="brand">
        <img src="@/assets/images/LOGO-ULEAM.png" alt="ULEAM" class="brand-logo" />
        <div class="brand-copy">
          <span class="brand-title">Sistema Odontológico</span>
          <span class="brand-subtitle">Facultad de Odontología · ULEAM</span>
        </div>
      </div>

      <div class="header-actions">
        <button
          @click="toggleTheme"
          class="theme-btn"
          :title="isDark() ? 'Cambiar a tema claro' : 'Cambiar a tema oscuro'"
          aria-label="Cambiar tema"
        >
          <i :class="isDark() ? 'fas fa-sun' : 'fas fa-moon'"></i>
        </button>

        <button v-if="authStore.user" @click="handleGoProfile" class="user-menu" aria-label="Ir a mi perfil">
          <span class="avatar">{{ authStore.fullName.charAt(0).toUpperCase() }}</span>
          <span class="user-info">
            <span class="user-name">{{ authStore.fullName }}</span>
            <span class="user-role">{{ authStore.userRole }}</span>
          </span>
          <i class="fas fa-chevron-down user-chevron"></i>
        </button>

        <button @click="handleLogout" class="logout-btn" aria-label="Cerrar sesión">
          <i class="fas fa-sign-out-alt"></i>
          <span class="logout-text">Cerrar Sesión</span>
        </button>
      </div>
    </div>
  </header>
</template>

<style scoped>
.app-header {
  background: var(--color-surface);
  border-bottom: 1px solid var(--color-border);
  box-shadow: var(--shadow-sm);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
  height: 64px;
  width: 100%;
  max-width: 100vw;
  overflow: hidden;
  box-sizing: border-box;
}

.header-content {
  height: 100%;
  width: 100%;
  max-width: 100%;
  padding: 0 1.25rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.75rem;
  box-sizing: border-box;
  overflow: hidden;
}

.brand {
  display: flex;
  align-items: center;
  gap: 0.625rem;
  user-select: none;
  flex-shrink: 0;
}

.brand-logo {
  width: 38px;
  height: 38px;
  object-fit: contain;
}

.brand-copy {
  display: flex;
  flex-direction: column;
  line-height: 1.1;
}

.brand-title {
  font-size: 0.95rem;
  font-weight: 700;
  color: var(--color-primary);
}

.brand-subtitle {
  margin-top: 0.25rem;
  font-size: 0.68rem;
  color: var(--color-text-muted);
  text-transform: uppercase;
  letter-spacing: 0.055em;
}

.brand-title,
.brand-subtitle {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  flex-shrink: 0;
}

.theme-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  border: none;
  background: var(--color-background-tertiary);
  color: var(--color-text-primary);
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: var(--transition-base);
  flex-shrink: 0;
}

.theme-btn:hover {
  background: var(--color-border);
  transform: scale(1.05);
}

.theme-btn i {
  font-size: 1.125rem;
}

.user-menu {
  display: flex;
  align-items: center;
  gap: 0.625rem;
  min-width: 190px;
  border: 1px solid var(--color-border);
  border-radius: 999px;
  background: var(--color-background-secondary);
  padding: 0.25rem 0.5rem 0.25rem 0.25rem;
  color: var(--color-text-primary);
  cursor: pointer;
  transition: var(--transition-base);
}

.user-menu:hover {
  border-color: var(--color-primary-light);
  background: var(--color-surface);
}

.avatar {
  width: 34px;
  height: 34px;
  border-radius: 50%;
  display: grid;
  place-items: center;
  background: var(--gradient-primary);
  color: white;
  font-weight: 700;
  flex-shrink: 0;
}

.user-info {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  min-width: 0;
  flex: 1;
}

.user-name {
  font-size: 0.9rem;
  font-weight: 600;
  color: var(--color-text-primary);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 150px;
}

.user-role {
  font-size: 0.75rem;
  color: var(--color-text-secondary);
  text-transform: capitalize;
  letter-spacing: 0.05em;
  white-space: nowrap;
}

.user-chevron {
  color: var(--color-text-muted);
  font-size: 0.65rem;
}

.logout-btn {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem 0.85rem;
  border-radius: var(--border-radius);
  border: 1px solid var(--color-border);
  background: transparent;
  color: var(--color-text-primary);
  cursor: pointer;
  font-size: 0.85rem;
  font-weight: 500;
  transition: var(--transition-base);
  flex-shrink: 0;
}

.logout-btn:hover {
  background: var(--color-error);
  color: white;
  border-color: var(--color-error);
}

@media (max-width: 1024px) {
  .app-header {
    left: 60px;
    width: calc(100% - 60px);
  }

  .header-content {
    padding: 0 0.75rem;
  }

  .user-name {
    max-width: 110px;
  }
}

@media (max-width: 768px) {
  .app-header {
    height: 60px;
    left: 56px;
    width: calc(100% - 56px);
  }

  .brand-subtitle,
  .user-info,
  .user-chevron,
  .logout-text {
    display: none;
  }

  .brand-copy {
    max-width: 150px;
  }

  .user-menu,
  .logout-btn {
    min-width: 0;
    padding: 0.25rem;
    width: 36px;
    height: 36px;
    justify-content: center;
    border-radius: 50%;
  }

  .theme-btn {
    width: 36px;
    height: 36px;
  }
}

@media (max-width: 480px) {
  .app-header {
    left: 52px;
    width: calc(100% - 52px);
  }

  .header-content {
    gap: 0.375rem;
    padding: 0 0.5rem;
  }

  .brand-copy {
    max-width: 110px;
  }

  .brand-title {
    font-size: 0.78rem;
  }

  .theme-btn,
  .user-menu,
  .logout-btn {
    width: 32px;
    height: 32px;
  }

  .theme-btn i,
  .user-menu i,
  .logout-btn i {
    font-size: 0.9rem;
  }
}
</style>
