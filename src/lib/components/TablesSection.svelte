<script lang="ts">
  import type { Table } from '$lib/types';
  import TableCard from './TableCard.svelte';

  let {
    tables = [],
    baseZIndex = 0,
    onAddPlayer,
    onSavePlayer,
    onDeleteTable,
    onEditTable,
    onExpandTable,
    onDeletePlayer,
    onOpenDetailPlayer
  } = $props();
</script>

<section aria-labelledby="tables-heading">
  <article class="card card-border" style={`z-index:${baseZIndex}`}>
    <div class="card-body gap-2 sm:gap-4 p-4 sm:p-8">
      <header class="flex items-center justify-between">
        <div>
          <h2 id="tables-heading" class="card-title">Tavoli attivi</h2>
          <p class="text-sm">Clicca sul titolo per vedere i dettagli.</p>
        </div>
        <span class="badge badge-outline"
          >{tables.length}
          <img src="/assets/icons/board-game.svg" alt="" class="h-4/5" aria-hidden="true" /></span
        >
      </header>

      {#if tables.length === 0}
        <div class="card card-border">
          <div class="card-body">
            <p>Nessun tavolo aperto: aprine uno e fai partire la serata!</p>
          </div>
        </div>
      {:else}
        <div class="grid gap-2 sm:gap-4 sm:grid-cols-2 lg:grid-cols-3 items-start">
          {#each [...tables].sort((a, b) => b.players.length - a.players.length) as table (table.id)}
            <TableCard
              {baseZIndex}
              {table}
              {onAddPlayer}
              {onSavePlayer}
              {onDeleteTable}
              {onEditTable}
              {onExpandTable}
              {onDeletePlayer}
              {onOpenDetailPlayer}
            />
          {/each}
        </div>
      {/if}
    </div>
  </article>
</section>
