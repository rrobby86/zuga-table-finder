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
  <dialog
    class="modal modal-open"
    style={`z-index:${zIndex}`}
    open
    onclick={handleDialogClick}
    oncancel={handleCancel}
  >
    <div class="modal-box space-y-2">
      <div class="flex items-center justify-between rounded-box bg-base-200 px-3 py-2">
        <h3 class="card-title truncate" style="max-width: 14ch;">{table.title}</h3>
        <div class="flex items-center gap-1">
          <div class="dropdown dropdown-end">
            <button class="btn btn-ghost btn-sm" aria-label="Azioni tavolo">
              <DotsThreeOutlineVerticalIcon size={18} weight="bold" aria-hidden="true" />
            </button>
            <ul class="dropdown-content menu bg-base-200 rounded-box z-50 p-2 shadow">
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
      <div class="space-y-0.5">
        <p class="text-sm w-full px-3 py-0 italic whitespace-pre-wrap break-words">
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

        <div class="divider"></div>

        {#if table.players.length === 0}
          <span class="badge badge-outline">Tavolo vuoto: invita qualcuno a giocare!</span>
        {:else}
          <div class="overflow-x-auto">
            <table class="table table-sm">
              <tbody>
                {#each [...table.players].sort((a, b) => (b.isTeacher ? 1 : 0) - (a.isTeacher ? 1 : 0)) as player}
                  {@const playerBadge = getPlayerBadgeStyle(player.isBeginner, player.isTeacher)}
                  <tr>
                    <td class="font-medium">
                      <button
                        type="button"
                        class="p-0 m-0 min-h-0 h-auto text-base font-medium hover:underline focus:underline focus-visible:outline-none focus-visible:ring truncate"
                        style="max-width: 14ch;"
                        aria-label={`Dettagli ${player.name}`}
                        onclick={() => handleOpenDetailPlayer(player)}
                      >
                        {player.name}
                      </button>
                    </td>
                    <td>
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
                    <td class="text-right">
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

      <div class="divider"></div>

      <div class="card-actions items-center justify-center">
        <button
          class="btn btn-md btn-primary btn-rounded"
          aria-label="Aggiungi player"
          onclick={handleAddPlayer}
        >
          <UserPlusIcon size={22} weight="bold" aria-hidden="true" />
        </button>
      </div>
    </div>
  </dialog>
{/if}
