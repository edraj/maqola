<!-- routify:meta reset -->
<script lang="ts">
import Sidebar from './sidebar.svelte'
import {Container} from "sveltestrap";
</script>

<!--<div class="d-flex row">-->
<!--&lt;!&ndash;  <div class="col-2">&ndash;&gt;-->
<!--&lt;!&ndash;    <Sidebar />&ndash;&gt;-->
<!--&lt;!&ndash;  </div>&ndash;&gt;-->
<!--  <div class="col-10">-->
<!--    <slot/>-->
<!--  </div>-->
<!--</div>-->

<Container>
  <slot/>
</Container>


<style>
    @import "prismjs/themes/prism.css";
    @import "prismjs/themes/prism-coy.css";
</style>
