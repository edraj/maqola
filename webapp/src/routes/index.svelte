<script lang="ts">
    import Dmart, { type QueryRequest, QueryType } from "@edraj/tsdmart";
    import { goto } from "@roxi/routify";

    let posts = [];
    (async () => {
        const query:  QueryRequest = {
            type: QueryType.subpath,
            space_name: 'maqola',
            subpath: "posts",
            search: "",
            limit: 100,
        };

        const response = await Dmart.query(query, 'public');
        posts = response.records;
        console.log({response});
    })();

    function handleClick(e: any, post: any) {
        e.preventDefault();
        console.log(post);
        $goto("/:post", {post: post.shortname});
        console.log(post.shortname);
        return;
    }
</script>

<h1 class="my-5" style="text-align: center;">Welcome to Maqola</h1>


<div class="container">
  <div class="row">
    {#each posts as post}
      <div class="col-12 col-md-6 col-lg-4 col-xl-3 col-xxl-2 my-3">
        <!-- svelte-ignore a11y-no-static-element-interactions -->
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <div style="cursor: pointer" class="card h-100" on:click={(e) => handleClick(e, post)}>
          <div class="card-body">
            <h5  style="text-align: center;" class="card-title">{post.shortname}</h5>
          </div>
        </div>
      </div>
    {/each}
  </div>
</div>
