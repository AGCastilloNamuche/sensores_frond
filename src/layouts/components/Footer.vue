<template>
  <div class="h-100 d-flex align-center justify-space-between">
    <!-- 👉 Footer: left content -->
    <span class="d-flex align-center">
      COPYRIGHT
      &copy;
      {{ new Date().getFullYear() }}
      <a href="https://agrovisioncorp.com/" target="_blank" rel="noopener noreferrer" style="color: #45B39D;" class="ms-1">Agrovision
        Perú SAC</a>
    </span>
    <!-- 👉 Footer: right content -->
    <span class="d-md-flex gap-x-4 d-none" style="color: #45B39D;">
      <span>Agricultura de precisión<VIcon color="#45B39D" icon="tabler-code"></VIcon></span>
    </span>
  </div>
</template>
