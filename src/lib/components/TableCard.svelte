<script lang="ts">
  import type { Table } from '$lib/types';
  import {
    PencilSimpleIcon,
    UserPlusIcon,
    ConfettiIcon,
    FeatherIcon,
    PuzzlePieceIcon,
    SkullIcon,
    GraduationCapIcon
  } from 'phosphor-svelte';
  import { getPlayerBadgeStyle } from '$lib/utils/player';
  import type { Player } from '$lib/types';
  let {
    table = null,
    baseZIndex = 0,
    onAddPlayer,
    onSavePlayer = () => {},
    onDeleteTable,
    onEditTable,
    onExpandTable,
    onDeletePlayer,
    onOpenDetailPlayer
  } = $props();

  const handleExpandTable = () => {
    onExpandTable(table.id);
  };

  const handleEditTable = () => {
    onEditTable(table, baseZIndex);
  };

  const handleDeleteTable = () => {
    onDeleteTable(table, baseZIndex);
  };

  const handleAddPlayer = () => {
    onAddPlayer(table);
  };

  const handleOpenDetailPlayer = (player: Player) => {
    onOpenDetailPlayer(table.id, player);
  };
</script>

<article class="card bg-base-100 card-border border-base-300 overflow-visible transition hover:shadow-lg w-full">
  <div class="border-base-300 ">
    <div class="flex items-center justify-between gap-2 p-4 pb-2">
      <button
        type="button"
        class="card-title text-base flex-1 min-w-0 px-2 text-left hover:underline focus:underline focus-visible:outline-none focus-visible:ring flex items-center gap-1"
        aria-label={`Apri dettagli ${table.title}`}
        onclick={handleExpandTable}
      >
        {#if table.weight === 'Party'}
          <ConfettiIcon
            size={20}
            weight="fill"
            class="text-warning pointer-events-none shrink-0"
          />
        {:else if table.weight === 'Leggero (max 45 min)'}
          <FeatherIcon size={20} weight="fill" class="text-info pointer-events-none shrink-0" />
        {:else if table.weight === 'Medio (1-2h)'}
          <PuzzlePieceIcon
            size={20}
            weight="fill"
            class="text-success pointer-events-none shrink-0"
          />
        {:else if table.weight === 'Estremo (>2h)'}
          <SkullIcon size={20} weight="fill" class="text-error pointer-events-none shrink-0" />
        {/if}
        <span class="truncate" style="max-width: 14ch;">{table.title}</span>
      </button>
      <div class="flex items-center gap-2 shrink-0">
        <span
          class={`badge ${table.players.length > table.seats ? 'badge-warning' : 'badge-primary'} badge-soft`}
        >
          {table.players.length}/{table.seats}
        </span>
        <button
          class="btn btn-sm btn-ghost"
          aria-label="Modifica tavolo"
          onclick={handleEditTable}
          type="button"
        >
          <PencilSimpleIcon size={18} weight="bold" aria-hidden="true" />
        </button>
      </div>
    </div>
  </div>

  <div class="card-body gap-4 pt-2 pb-4">
    <div class="flex flex-wrap items-center gap-2">
      {#if table.players.length === 0}
        <span class="badge badge-outline">Tavolo vuoto :(</span>
      {:else}
        {#each [...table.players].sort((a, b) => (b.isTeacher ? 1 : 0) - (a.isTeacher ? 1 : 0)) as player}
          {@const playerBadge = getPlayerBadgeStyle(player.isBeginner, player.isTeacher)}
          <button
            type="button"
            class={`${player.isTeacher ? 'badge badge-accent' : playerBadge.className} gap-1 hover:badge-outline focus-visible:outline-none focus-visible:ring flex items-center`}
            title={player.isTeacher ? 'Spiegatore' : player.isBeginner ? 'Principiante' : 'Esperto'}
            aria-label={`Dettagli ${player.name}`}
            onclick={() => handleOpenDetailPlayer(player)}
          >
            <playerBadge.Icon size={14} weight="fill" aria-hidden="true" class="shrink-0" />
            <span class="truncate" style="max-width: 14ch;">
              {player.name}
            </span>
          </button>
        {/each}
      {/if}
      <button
        class="btn btn-md btn-primary btn-circle hover:scale-110 transition-transform"
        aria-label="Aggiungi player"
        onclick={handleAddPlayer}
      >
        <UserPlusIcon size={22} weight="bold" aria-hidden="true" />
      </button>
    </div>
  </div>
</article>
