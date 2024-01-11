<script>
  import { flip } from "svelte/animate";
  import { fade } from "svelte/transition";
  import { dndzone } from "svelte-dnd-action";

  import { clickOutside } from "lib/actions/clickOutside";
  import Bars3SvgIcon from "lib/svg-icons/Bars3SvgIcon.svelte";

  import EditForm from "./EditForm.svelte";
  import OptionsMenu from "./OptionsMenu.svelte";

  export let title;
  export let itemsStore;
  export let toggleCompleted;
  export let updateItem;
  export let deleteItem;
  export let handleConsider;
  export let handleFinalize;
  export let dragDisabled;
  export let handleStartDrag;
  export let handleDragKeyDown;
  export let flipDurationMs;
  export let noItemsMessage;
  export let optionsMenuClass;
</script>

<ul
  class="min-h-[40px]"
  aria-label={title}
  use:dndzone={{
    items: $itemsStore,
    flipDurationMs,
    dragDisabled,
    morphDisabled: true,
    dropTargetStyle: {},
    dropTargetClasses: ["border-2", "border-dashed", "rounded-lg", "border-accent"],
  }}
  on:consider={(event) => handleConsider(event, itemsStore)}
  on:finalize={(event) => handleFinalize(event, itemsStore)}
>
  {#each $itemsStore as item (item.id)}
    <li
      class="flex items-center justify-between px-2 py-1.5 text-lg rounded-lg"
      aria-label={item.name}
      animate:flip={{ duration: flipDurationMs }}
    >
      {#if item.isEditing}
        <EditForm {item} {itemsStore} {updateItem} />
      {:else}
        <label
          class="flex items-center gap-3 grow cursor-pointer"
          class:opacity-50={item.completed}
        >
          <input
            class="checkbox bg-transparent"
            type="checkbox"
            checked={item.completed}
            on:change={() => toggleCompleted(item)}
          />

          <span class:line-through={item.completed}>{item.name}</span>
        </label>

        <div class="flex gap-1">
          <OptionsMenu {item} {itemsStore} {updateItem} {deleteItem} {optionsMenuClass} />

          <!-- Drag Handle. -->
          <button
            aria-label="drag-handle"
            class:cursor-grab={dragDisabled}
            class:cursor-grabbing={!dragDisabled}
            on:mousedown={handleStartDrag}
            on:touchstart={handleStartDrag}
            on:keydown={handleDragKeyDown}
            use:clickOutside={() => (dragDisabled = true)}
          >
            <Bars3SvgIcon className="w-6 h-6" />
          </button>
        </div>
      {/if}
    </li>
  {:else}
    <p class="flex items-center h-10 px-2" in:fade={{ delay: 250 }}>{noItemsMessage}</p>
  {/each}
</ul>