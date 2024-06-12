<script lang="ts">
    import {
        Slider,
        TextInput,
        Checkbox,
        Button,
        Dropdown,
    } from "carbon-components-svelte";
    import { onMount } from "svelte";

    let font_size: number = 30;
    let color: string = "white";
    let fullscreen: boolean = false;
    let name: string = "Untitled";
    let text: string = "";
    let id: string = "" + Math.random() + "." + Math.random();
    let items: {
        id: string;
        name: string;
        color: string;
        font_size: number;
        text: string;
    }[] = [];
    let select = "-1";

    const fullscreenchanged = (e: any) => {
        fullscreen = document.fullscreenElement != null;
    };

    const map = (item: any) => {
        return item["name"];
    };

    onMount(() => {
        items = JSON.parse(localStorage.getItem("text.sh") ?? "[]");
        document.addEventListener("fullscreenchange", fullscreenchanged);
        return () => {
            document.removeEventListener("fullscreenchange", fullscreenchanged);
        };
    });
</script>

<div class="a">
    <div class="b">
        <div class="c">
            <Slider
                labelText="Font size (px)"
                min={10}
                max={300}
                maxLabel="300 px"
                step={1}
                bind:value={font_size}
            />
            <TextInput labelText="Text color" bind:value={color}></TextInput>
            <Checkbox
                on:check={(e) => {
                    fullscreen = e.detail;
                    if (!document.fullscreenElement && fullscreen) {
                        document.documentElement.requestFullscreen();
                    } else if (document.exitFullscreen) {
                        document.exitFullscreen().catch(() => {});
                    }
                }}
                bind:checked={fullscreen}
                labelText="Fullscreen"
            ></Checkbox>
        </div>
        <div class="d">
            <div class="flex">
                <TextInput labelText="Save Name" bind:value={name}></TextInput>
                <div style="margin-top: auto;">
                    <Button
                        size="field"
                        on:click={() => {
                            const i = items.findIndex((e) => e.id == id);
                            if (i >= 0) {
                                items[i] = {
                                    id,
                                    name,
                                    font_size,
                                    color,
                                    text,
                                };
                            } else {
                                items.push({
                                    id,
                                    name,
                                    font_size,
                                    color,
                                    text,
                                });
                            }
                            localStorage.setItem(
                                "text.sh",
                                JSON.stringify(items),
                            );
                        }}
                    >
                        Save
                    </Button>
                </div>
            </div>
            <div class="flex">
                <Dropdown
                    style="width:100%"
                    itemToString={map}
                    titleText="Load save"
                    bind:selectedId={select}
                    on:select={(e) => {
                        console.log(select, items);
                        const i = items.findIndex((e) => e.id == select);
                        if (i >= 0) {
                            id = items[i].id;
                            name = items[i].name;
                            font_size = items[i].font_size;
                            color = items[i].color;
                            text = items[i].text;
                        }
                    }}
                    {items}
                ></Dropdown>
                <div style="margin-top: auto;">
                    <Button
                        size="field"
                        on:click={() => {
                            id = "" + Math.random() + "." + Math.random();
                            name = "Untitled " + items.length;
                            font_size = 30;
                            color = "white";
                            text = "";
                            items.push({
                                id,
                                name,
                                font_size,
                                color,
                                text,
                            });
                        }}>New</Button
                    >
                </div>
            </div>
        </div>
    </div>
    <textarea
        class:fullscreen
        style="font-size: {font_size}px;color: {color};"
        bind:value={text}
    ></textarea>
    <div
        class="e"
        class:fullscreen
        style="font-size: {font_size}px;color: {color};white-space: pre-wrap"
    >
        {text}
    </div>
</div>

<style>
    .a {
        display: flex;
        height: 100%;
        flex-direction: column;
    }
    .b {
        display: flex;
        padding: 20px;
        gap: 10px;
    }
    .c,
    .d {
        display: flex;
        flex-direction: column;
        width: 50%;
        gap: 6px;
    }
    .e {
        display: none;
    }
    .e.fullscreen {
        display: block;
        position: fixed;
        top: 0;
        left: 0;
        z-index: 9999;
        padding-top: 50px;
        width: 100%;
        height: 100%;
        background: #161616;
    }
    .flex {
        display: flex;
    }
    textarea {
        outline: none;
        background: #161616;
        resize: none;
        width: 100%;
        height: 100%;
        border: none;
        padding: 5px;
    }
    textarea.fullscreen {
        display: none;
    }
    :global(body > div.main) {
        width: 100%;
        height: 100vh;
        margin: 0;
    }
</style>
