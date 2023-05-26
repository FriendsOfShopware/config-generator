<script lang="ts">
    import {stringify} from "yaml";
    import Checkbox from "./components/Checkbox.svelte";
    import Textfield from "./components/Textfield.svelte";

    const applyConfig = (files: unknown, path: string, value: boolean | string | number) => {
        const parts = path.split(".");
        const last = parts.pop() as string;
        let obj = files as any;
        for (const part of parts) {
            if (!obj[part]) {
                obj[part] = {};
            }
            obj = obj[part];
        }
        obj[last] = value;
    };

    const renderFile = (name: string, value: unknown) => {
        const obj = {};
        // @ts-ignore
        obj[name] = value;
        return stringify(obj);
    };

    const values = {
        auto_update: false,

        update_mail_variables: false,

        cache_redis: false,
        cache_prefix: "",
        cache_redis_url: "redis://localhost",

        cart_compress: true,
        cart_redis_url: "",
        cart_expire: "30",

        admin_worker: false,
        admin_notification_worker: false,
        admin_queue_stats_worker: false,

        first_run_wizard: false,

        number_range_redis: false,
        number_range_redis_url: "redis://localhost",
    };

    const generateFiles = () => {
        const files = {};

        applyConfig(files, "shopware.auto_update.enabled", values.auto_update);

        if (values.cache_redis) {
            applyConfig(files, "framework.cache.default_redis_provider", values.cache_redis_url);
            applyConfig(files, "framework.cache.app", "cache.adapter.redis");

            if (values.cache_prefix) {
                applyConfig(files, "shopware.cache.redis_prefix", values.cache_prefix);
            }
        }

        applyConfig(files, "shopware.admin_worker.enable_admin_worker", values.admin_worker);
        applyConfig(files, "shopware.admin_worker.enable_notification_worker", values.admin_notification_worker);
        applyConfig(files, "shopware.admin_worker.enable_queue_stats_worker", values.admin_queue_stats_worker);

        if (!values.cart_compress) {
            applyConfig(files, "shopware.cart.compress", values.cart_compress);
        }

        if (values.cart_expire !== "30") {
            applyConfig(files, "shopware.cart.expire_days", parseInt(values.cart_expire));
        }

        if (values.cart_redis_url) {
            applyConfig(files, "shopware.cart.redis_url", values.cart_redis_url);
        }

        if (!values.first_run_wizard) {
            applyConfig(files, "shopware.store.frw", values.first_run_wizard);
        }

        if (values.number_range_redis) {
            applyConfig(files, "shopware.number_range.increment_storage", 'Redis');
            applyConfig(files, "shopware.number_range.redis_url", values.number_range_redis_url);
        }

        if (!values.update_mail_variables) {
            applyConfig(files, "shopware.mail.update_mail_variables_on_send", values.update_mail_variables);
        }

        return files;
    };
</script>

<svelte:head>
    <title>Shopware Config Generator</title>
    <meta name="description" content="Shopware Config Generator"/>
</svelte:head>
<section>
    <div class="row">
        <div class="col-md-8">
            <div class="card mt-3">
                <div class="card-body">
                    <h5 class="card-title">General</h5>
                    <p class="card-text">
                        <Checkbox
                                label="Auto update enabled"
                                bind:checked={values.auto_update}
                        />

                        <Checkbox
                                label="First Run Wizard enabled"
                                bind:checked={values.first_run_wizard}
                        />

                        <Checkbox
                                label="Enable Admin Worker"
                                bind:checked={values.admin_worker}
                        />

                        <Checkbox
                                label="Enable Notification Worker"
                                bind:checked={values.admin_notification_worker}
                        />

                        <Checkbox
                                label="Enable Queue Stats Worker"
                                bind:checked={values.admin_queue_stats_worker}
                        />

                        <Checkbox
                                label="Update Mail Variables on Send"
                                bind:checked={values.update_mail_variables}
                        />
                    </p>
                </div>
            </div>

            <div class="card mt-3">
                <div class="card-body">
                    <h5 class="card-title">Cache</h5>
                    <p class="card-text">
                        <Checkbox
                                label="Redis Cache"
                                bind:checked={values.cache_redis}
                        />

                        <Textfield
                                label="Redis Server"
                                bind:value={values.cache_redis_url}
                                disabled={!values.cache_redis}
                        />

                        <Textfield
                                label="Redis Prefix"
                                bind:value={values.cache_prefix}
                                disabled={!values.cache_redis}
                        />
                    </p>
                </div>
            </div>

            <div class="card mt-3">
                <div class="card-body">
                    <h5 class="card-title">Cart</h5>
                    <p class="card-text">
                        <Checkbox
                                label="Compress Cart"
                                bind:checked={values.cart_compress}
                        />

                        <Textfield
                                label="Redis URL"
                                bind:value={values.cart_redis_url}
                        />

                        <Textfield
                                label="Cart Expire in Days"
                                bind:value={values.cart_expire}
                        />
                    </p>
                </div>
            </div>

            <div class="card mt-3">
                <div class="card-body">
                    <h5 class="card-title">Number Ranges</h5>
                    <p class="card-text">
                        <Checkbox
                                label="Enable Redis"
                                bind:checked={values.number_range_redis}
                        />

                        <Textfield
                                label="Redis URL"
                                bind:value={values.number_range_redis_url}
                                disabled={!values.number_range_redis}
                        />
                    </p>
                </div>
            </div>

            <div class="card mt-3">
                <div class="card-body">
                    <h5 class="card-title">Filesystem</h5>
                    <p class="card-text">

                    </p>
                </div>
            </div>
        </div>

        <div class="col-md-4">
            <nav class="mt-3">
                <div class="nav nav-tabs" id="nav-configs" role="tablist">
                    {#each Object.keys(generateFiles(values)) as file, index}
                        <button
                                class="nav-link {index === 0 ? 'active' : ''}"
                                id="nav-{file}-tab"
                                data-bs-toggle="tab"
                                data-bs-target="#nav-{file}"
                                type="button"
                                role="tab"
                                aria-controls="nav-{file}"
                                aria-selected={index === 0}
                        >
                            {file}.yml
                        </button>
                    {/each}
                </div>
            </nav>

            <div class="tab-content">
                {#each Object.keys(generateFiles(values)) as file, index}
                    <div
                            class="tab-pane {index === 0 ? 'show active' : ''}"
                            id="nav-{file}"
                            role="tabpanel"
                            aria-labelledby="nav-{file}-tab"
                    >
                        <pre>{renderFile(file, generateFiles(values)[file])}</pre>
                    </div>
                {/each}
            </div>
        </div>
    </div>
</section>

<style>
</style>
