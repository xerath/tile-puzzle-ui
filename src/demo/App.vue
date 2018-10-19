<template>
    <div class="container-fluid">
        <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
        <dnd-grid-container
            :layout.sync="layout"
            :cellSize="cellSize"
            :maxColumnCount="maxColumnCount"
            :maxRowCount="maxRowCount"
            :margin="margin"
            :bubbleUp="bubbleUp"
        >
            <dnd-grid-box
                v-for="tile in layout"
                :boxId="tile.id"
                :key="tile.id"
                dragSelector="div"
            >
                <div class="card demo-box" :class="tile.type">
                    {{ tile.title }}
                </div>
            </dnd-grid-box>
        </dnd-grid-container>
    </div>
</template>

<style>
    .demo-box {
        width: 100%;
        height: 100%;
    }
</style>

<script>
    import { Container, Box } from '../components'

    export default {
        components: {
            DndGridContainer: Container,
            DndGridBox: Box
        },

        data () {

            let x,y;

            let map = [
                [ 0, 0, 0, 0, 0, 0 ],
                [ 0, 1, 2, 2, 3, 0 ],
                [ 0, 1, 2, 2, 3, 0 ],
                [ 0, 4, 5, 5, 6, 0 ],
                [ 0, 4, 7, 8, 6, 0 ],
                [ 0, 9, x, x, 10, 0 ],
                [ 0, 0, y, y, 0, 0 ]
            ]

            let tiles = [];
            map.forEach( (row, rowindex) => {
                row.forEach( (item, colindex) => {

                    if(item !== undefined) {

                        let tile = {
                            id: tiles.length,
                            title: String.fromCharCode(96 + item),
                            hidden: false,
                            pinned: !item,
                            position: {
                                x: colindex,
                                y: rowindex,
                                w: 1,
                                h: 1
                            }
                        }

                        if(item == 0) {
                            tile.type = 'block'
                            tile.title = 0
                            tiles.push(tile)
                        } else {

                            if(tiles[tile.title]) {
                                tiles[tile.title].position.w = (colindex - tiles[tile.title].position.x + 1)
                                tiles[tile.title].position.h = (rowindex - tiles[tile.title].position.y + 1)
                            } else {
                                // Add tile
                                tile.type = 'tile'
                                tiles[tile.title] = tile;
                                tiles.push(tile)
                            }

                        }
                    }

                })
            });

            return {
                cellSize: {
                    w: 50,
                    h: 50
                },
                maxColumnCount: 6,
                maxRowCount: 7,
                bubbleUp: false,
                margin: 5,
                layout: tiles
            }
        },

        computed: {
            layoutWithoutSettings () {
                return this.layout.filter((box) => {
                    return box.id !== 'settings'
                })
            }
        },

        methods: {
            onLayoutUpdate (evt) {
                this.layout = evt.layout
            }
        }
    }
</script>
