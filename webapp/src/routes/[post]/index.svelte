<script lang="ts">
    import Dmart, {type ActionRequest, type QueryRequest, QueryType, RequestType, ResourceType} from "@edraj/tsdmart";
    import { goto, params } from "@roxi/routify";
    import {Button, Card, CardBody, Icon, Input, Tooltip} from "sveltestrap";

    let title = $params.post;
    let selectedPost = null;
    let posts = [];

    async function fetchPosts(){
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
    }

    fetchPosts();

    let reactions = null;
    let shares = null;
    function handleClick(post) {
        if(selectedPost === post) {
            selectedPost = null;
            return;
        }
        selectedPost = post;
    }

    let comment = "";
    async function handleSetComment(){
        const request: ActionRequest = {
            space_name: "maqola",
            request_type: RequestType.create,
            records: [
                {
                    resource_type: ResourceType.comment,
                    shortname: "auto",
                    subpath: `posts/${title}/${selectedPost.shortname}`,
                    attributes: {
                        is_active: true,
                        state: "commented",
                        body: comment,
                    },
                }
            ]
        }
        const response = await Dmart.request(request)
        console.log({response})
        await fetchPosts();
        selectedPost = posts.filter((post) => post.uuid === selectedPost.uuid)[0];
    }

    $: {
        if(selectedPost == null) {
            reactions = null;
            shares = null;
        } else {
            reactions = (selectedPost?.attachments?.reaction ?? []).map((reaction: any) => reaction.attributes.author_locator.shortname);
            shares = (selectedPost?.attachments?.share ?? []).length;
        }
    }

</script>

<h1 class="my-5" style="text-align: center;">{title}</h1>
  <div class="row">

    <div class="col-2">
      {#each posts as post}
        <div class="my-3">
          <Card color={selectedPost?.uuid === post?.uuid ? "primary" : ""} style="cursor: pointer" class="h-100" on:click={() => handleClick(post)}>
            <CardBody>
              <h5 style={selectedPost?.uuid === post?.uuid ? "color:  white;!important" : ""}>
                {(post?.attributes?.displayname?.ar ?? post.shortname)}
              </h5>
            </CardBody>
          </Card>
        </div>
      {/each}
    </div>

    <div class="col-10 my-3">
      {#if selectedPost}
        <Card>
          <CardBody>
            {@html selectedPost.attributes?.payload?.body}
          </CardBody>
        </Card>
        <Card>
          <CardBody class="d-flex justify-content-between">
            <div class="d-flex align-items-center">
              <Icon class="mx-1" id="reactions" name="heart-fill" />
              <Tooltip
                      animation
                      delay={10}
                      placement="top"
                      target="reactions">
                {reactions?.join(", ")}
              </Tooltip>
              <Icon class="mx-1" id="shares" name="share-fill" />
              <Tooltip
                      animation
                      delay={10}
                      placement="top"
                      target="shares">
                {shares}
              </Tooltip>
            </div>
            <div class="d-flex row">
              <div class="col-10"><Input placeholder="Comment" bind:value={comment} /></div>
              <div class="col-2" on:click={handleSetComment}><Button>comment</Button></div>
            </div>
          </CardBody>
        </Card>

        <Card>
          <CardBody>
            {#each (selectedPost?.attachments?.comment ?? []) as comment}
              <Card>
                <CardBody>
                  <h5>{comment.attributes.owner_shortname}</h5>
                  <p>{comment.attributes.body}</p>
                </CardBody>
              </Card>
            {/each}
          </CardBody>
        </Card>
      {/if}
    </div>

  </div>


