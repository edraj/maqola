<script context="module" lang="ts">
    import { Router, createRouter } from "@roxi/routify";
    import routes from "../.routify/routes.default";
    import { SvelteToast } from "@zerodevx/svelte-toast";
    import Dmart from "@edraj/tsdmart";
    import {website} from "./config"

    Dmart.baseURL = website.backend;

    const options = {
        duration: 2500,
        initial: 1,
        next: 0,
        pausable: false,
        dismissable: true,
        reversed: false,
        intro: { x: 256 },
        theme: {
            "--toastColor": "mintcream",
        },
        classes: ["custom-toast"],
    };
</script>

<script lang="ts">
    import { setupI18n, dir, locale } from "./i18n";

    function findRoute(routers: any, paths: any, lang: any) {
        if (paths.length === 0) {
            return routers;
        }

        const [currentPath, ...remainingPaths] = paths;

        const matchingChild = routers.children.find(
            (child: any) => child.name === `${currentPath}`
        );
        if (matchingChild) {
            return findRoute(matchingChild, remainingPaths, lang);
        } else {
            return null;
        }
    }
    async function prepareRouter(lang: any) {
        return createRouter({
            routes: routes,
            urlRewrite: {
                toInternal: (url) => {

                    const paths = url.split("/");
                    paths.shift();
                    let fileName = paths[paths.length - 1];
                    if (fileName === "") {
                        fileName = "index";
                    }

                    if (![".en", ".ar", ".kd"].includes(fileName)) {
                        paths[paths.length - 1] = `${fileName}.${lang}`;
                        let result = findRoute(routes, paths, lang);
                        // if the REQUIRED+LANG file is NOT found
                        // then look for the REQUIRED file
                        if (result === null) {
                            paths[paths.length - 1] = fileName;
                            result = findRoute(routes, paths, lang);
                            // if the REQUIRED file is NOT found
                            // then look for the INDEX+LANG file
                            if (result === null) {
                                paths[paths.length - 1] = `index.${lang}`;
                                result = findRoute(routes, paths, lang);
                                // if the INDEX+LANG file is NOT found
                                // then look for the INDEX file
                                if (result === null) {
                                    paths[paths.length - 1] = `index`;
                                    result = findRoute(routes, paths, lang);
                                    if (result !== null) {
                                        // return INDEX
                                        return (
                                            url
                                                .split("/")
                                                .slice(0, url.split("/").length - 1)
                                                .join("/") + `/${paths[paths.length - 1]}`
                                        );
                                    }
                                } else {
                                    // return INDEX+LANG
                                    return (
                                        url
                                            .split("/")
                                            .slice(0, url.split("/").length - 1)
                                            .join("/") + `/${paths[paths.length - 1]}`
                                    );
                                }
                            } else {
                                // return REQUIRED
                                return (
                                    url
                                        .split("/")
                                        .slice(0, url.split("/").length - 1)
                                        .join("/") + `/${paths[paths.length - 1]}`
                                );
                            }
                        } else {
                            // return REQUIRED+LANG
                            return (
                                url
                                    .split("/")
                                    .slice(0, url.split("/").length - 1)
                                    .join("/") + `/${paths[paths.length - 1]}`
                            );
                        }
                    }

                    return url;
                },
                toExternal: (url) => url,
            },
        });
    }

    setupI18n();

    $: {
        if (typeof document != "undefined") {
            document.dir = $dir;
        }
    }
</script>

<svelte:head>
  <link rel="stylesheet" media="screen" href="{new URL('bootstrap-icons/font/bootstrap-icons.min.css', import.meta.url).href}">
  <link rel="stylesheet" media="screen" href="{new URL('./app.css', import.meta.url).href}">
  {#if $dir === "rtl"}
    <link rel="stylesheet" id="bootstrap" href={new URL("bootstrap/dist/css/bootstrap.rtl.min.css", import.meta.url).href} />
  {:else}
    <link rel="stylesheet" id="bootstrap" href={new URL("bootstrap/dist/css/bootstrap.min.css", import.meta.url).href} />
  {/if}
</svelte:head>

<div id="routify-app">
  <SvelteToast {options} />
  {#await prepareRouter($locale) then router}
    <Router {router} />
  {/await}
</div>
