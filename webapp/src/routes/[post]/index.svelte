<!--<script lang="ts">-->
<!--    import Dmart, { type QueryRequest, QueryType } from "@edraj/tsdmart";-->
<!--    import { goto, params } from "@roxi/routify";-->

<!--    let title = $params.post_name;-->
<!--    let posts = [];-->
<!--    (async () => {-->
<!--        const query:  QueryRequest = {-->
<!--            type: QueryType.subpath,-->
<!--            space_name: 'maqola',-->
<!--            subpath: "posts/" + title,-->
<!--            search: "",-->
<!--            limit: 100,-->
<!--        };-->

<!--        const response = await Dmart.query(query, 'public');-->
<!--        posts = response.records;-->
<!--        console.log(response);-->
<!--    })();-->

<!--    function handleClick(post) {-->
<!--    }-->
<!--</script>-->

<!--<h1 class="my-5" style="text-align: center;">{title}</h1>-->


<!--<div class="container">-->
<!--  <div class="row">-->
<!--    {#each posts as post}-->
<!--      <div class="col-12 col-md-6 col-lg-4 col-xl-3 col-xxl-2 my-3">-->
<!--        <div style="cursor: pointer" class="card h-100" on:click={() => handleClick(post)}>-->
<!--          <div class="card-body">-->
<!--            <h5  style="text-align: center;" class="card-title">{post.shortname}</h5>-->
<!--          </div>-->
<!--        </div>-->
<!--      </div>-->
<!--    {/each}-->
<!--  </div>-->
<!--</div>-->


<script>
    import { params } from "@roxi/routify";
</script>

<h1 class="my-5" style="text-align: center;">Welcome to {$params.post}</h1>