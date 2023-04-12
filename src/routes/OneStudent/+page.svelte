<script>
  // @ts-nocheck

  import supabase from "$lib/db";
  import { onMount } from "svelte";
  import { goto } from "$app/navigation";

</script>

<center>
  <button on:click={() => goto("/")}>Back Home</button>
  <h1>One Student View</h1><br>
  
</center>