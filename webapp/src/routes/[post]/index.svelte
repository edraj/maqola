<script lang="ts">
    import Dmart, { type QueryRequest, QueryType } from "@edraj/tsdmart";
    import { goto, params } from "@roxi/routify";
    import {Card, CardBody} from "sveltestrap";

    let title = $params.post;
    let selectedPost = null;
    let posts = [];
    (async () => {
        const query:  QueryRequest = {
            type: QueryType.subpath,
            space_name: 'maqola',
            subpath: "posts/" + title,
            search: "",
            limit: 100,
        };

        const response = await Dmart.query(query, 'public');
        posts = response.records;
        console.log(response);
    })();

    function handleClick(post) {
        if(selectedPost === post) {
            selectedPost = null;
            return;
        }
        selectedPost = post;
    }
</script>

<h1 class="my-5" style="text-align: center;">{title}</h1>


<div class="container">
  <div class="row">
    {#each posts as post}
      <div class="col-12 col-md-6 col-lg-4 col-xl-3 col-xxl-2 my-3">
        <Card color={selectedPost?.uuid === post?.uuid ? "primary" : ""} style="cursor: pointer" class="h-100" on:click={() => handleClick(post)}>
          <CardBody>
            <h5  style="text-align: center;" class="card-title">{post.shortname}</h5>
          </CardBody>
        </Card>
      </div>
    {/each}

    {#if selectedPost}
      {@html selectedPost.attributes?.payload?.body}
    {/if}
  </div>
</div>

