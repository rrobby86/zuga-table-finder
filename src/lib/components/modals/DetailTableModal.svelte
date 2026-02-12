<script lang="ts">
  import type { Table, Player } from '$lib/types';
  import {
    PencilSimpleIcon,
    TrashIcon,
    XIcon,
    UserPlusIcon,
    DotsThreeOutlineVerticalIcon
  } from 'phosphor-svelte';
  import { getPlayerBadgeStyle } from '$lib/utils/player';
  let {
    open = false,
    zIndex = 0,
    table = null,
    close,
    onAddPlayer,
    onDeleteTable,
    onEditTable,
    onDeletePlayer,
    onOpenDetailPlayer
  } = $props();

  const handleDialogClick = (e: MouseEvent) => {
    if (e.target === e.currentTarget) close();
  };

  const handleCancel = () => {
    close();
  };

  const handleClose = () => {
    close();
  };

  const handleEditTable = () => {
    onEditTable(table, zIndex);
  };

  const handleDeleteTable = () => {
    onDeleteTable(table, zIndex);
  };

  const handleDeletePlayerButton = (player: Player) => {
    onDeletePlayer(table.id, player.id, player.name);
  };

  const handleAddPlayer = () => {
    onAddPlayer(table);
  };

  const handleOpenDetailPlayer = (player: Player) => {
    onOpenDetailPlayer(table.id, player);
  };
</script>

{#if open && table}
  <dialog class="modal modal-open" style={`z-index:${zIndex}`} onclick={handleDialogClick}>
    <div
      class="card bg-base-100 card-border border-base-300 overflow-hidden mx-4"
      style="width: calc(100% - 4rem); max-width: 42rem;"
    >
      <div class="border-base-300 border-b border-dashed">
        <div class="flex items-center justify-between gap-2 p-4">
          <h3 class="card-title text-base px-2 truncate" style="max-width: 14ch;">{table.title}</h3>
          <div class="flex items-center gap-1 shrink-0">
            <div class="dropdown dropdown-end">
              <button class="btn btn-ghost btn-sm" aria-label="Azioni tavolo">
                <DotsThreeOutlineVerticalIcon size={18} weight="bold" aria-hidden="true" />
              </button>
              <ul class="dropdown-content menu bg-neutral rounded-box z-50 p-2 shadow-sm">
                <li>
                  <button
                    onclick={handleEditTable}
                    class="btn btn-ghost hover:bg-base-300 focus:bg-base-300 focus-visible:outline-none focus-visible:ring"
                  >
                    <PencilSimpleIcon size={16} weight="bold" aria-hidden="true" />
                    Modifica
                  </button>
                </li>
                <li>
                  <button
                    onclick={handleDeleteTable}
                    class="text-error hover:bg-error hover:text-error-content"
                  >
                    <TrashIcon size={16} weight="bold" aria-hidden="true" />
                    Elimina
                  </button>
                </li>
              </ul>
            </div>
            <button
              class="btn btn-sm btn-ghost"
              aria-label="Chiudi dettagli"
              onclick={handleClose}
              type="button"
            >
              <XIcon size={18} weight="bold" aria-hidden="true" />
            </button>
          </div>
        </div>
        <div class="card-body gap-2 pt-4">
          <p class="text-sm w-full italic whitespace-pre-wrap break-words">
            {table.description || 'Nessuna descrizione.'}
          </p>
          <div class="flex items-center justify-center gap-2 text-xs">
            <span class="font-semibold">Peso:</span>
            <span class="">{table.weight}</span>
          </div>
          <div class="flex items-center justify-center gap-2 text-xs">
            <span class="font-semibold">Giocatori:</span>
            <span class="">{table.players.length}/{table.seats}</span>
          </div>
          <div class="flex items-center justify-center gap-2 text-xs">
            <span class="font-semibold">Aggiornato il:</span>
            <span class="">
              {new Date(table.createdAt).toLocaleString('it-IT', {
                dateStyle: 'medium',
                timeStyle: 'short'
              })}
            </span>
          </div>

          <div class="divider my-0"></div>
          {#if table.players.length === 0}
            <div>
              <span class="badge badge-outline">Tavolo vuoto: invita qualcuno a giocare!</span>
            </div>
          {:else}
            <div>
              <table class="table table-sm w-full ">
                <tbody>
                  {#each [...table.players].sort((a, b) => (b.isTeacher ? 1 : 0) - (a.isTeacher ? 1 : 0)) as player}
                    {@const playerBadge = getPlayerBadgeStyle(player.isBeginner, player.isTeacher)}
                    <tr>
                      <td class="text-sm px-2 py-1">
                        <button
                          type="button"
                          class="p-0 m-0 min-h-0 h-auto text-base text-sm hover:underline focus:underline focus-visible:outline-none focus-visible:ring truncate"
                          style="max-width: 14ch;"
                          aria-label={`Dettagli ${player.name}`}
                          onclick={() => handleOpenDetailPlayer(player)}
                        >
                          {player.name}
                        </button>
                      </td>
                      <td class="px-2 py-1 text-sm">
                        <span
                          class={playerBadge.className}
                          title={player.isTeacher
                            ? 'Spiegatore'
                            : player.isBeginner
                              ? 'Principiante'
                              : 'Esperto'}
                        >
                          <playerBadge.Icon size={14} weight="fill" aria-hidden="true" />
                          <span
                            >{player.isTeacher
                              ? 'Spiegatore'
                              : player.isBeginner
                                ? 'Principiante'
                                : 'Esperto'}</span
                          >
                        </span>
                      </td>
                      <td class="text-right px-2 py-1">
                        <button
                          class="btn btn-xs btn-ghost btn-error focus-visible:outline-none focus-visible:ring"
                          aria-label={`Rimuovi ${player.name}`}
                          onclick={() => handleDeletePlayerButton(player)}
                        >
                          <TrashIcon size={16} weight="bold" aria-hidden="true" />
                        </button>
                      </td>
                    </tr>
                  {/each}
                </tbody>
              </table>
            </div>
          {/if}
        </div>

        <div class="divider my-0"></div>

        <div class="card-actions items-center justify-center p-2">
          <button
            class="btn btn-md btn-primary btn-circle hover:scale-120 transition-transform"
            aria-label="Aggiungi player"
            onclick={handleAddPlayer}
          >
            <UserPlusIcon size={22} weight="bold" aria-hidden="true" />
          </button>
        </div>
      </div>
    </div>
    <button type="button" class="modal-backdrop" onclick={close} aria-label="Chiudi"></button>
  </dialog>
{/if}
