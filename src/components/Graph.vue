<template>
    <div class="p-5">
        <div class="flex justify-center items-center w-full">
            <div class="flex gap-5 px-4 py-2 border border-black rounded-md">
                <span class="inline-block ps-[0.15rem]">
                        Default
                </span>
                <label class="relative inline-flex cursor-pointer items-center">
                    <input @change="toggleShowMode" id="switch" type="checkbox" class="peer sr-only"/>
                    <label for="switch" class="hidden"></label>
                    <div
                        class="peer h-6 w-11 rounded-full border bg-slate-200 after:absolute after:left-[2px] after:top-0.5 after:h-5 after:w-5 after:rounded-full after:border after:border-gray-300 after:bg-white after:transition-all after:content-[''] peer-checked:bg-slate-800 peer-checked:after:translate-x-full peer-checked:after:border-white peer-focus:ring-green-300"></div>
                </label>
                <span class="inline-block ps-[0.15rem]">
                        Changed
                </span>
            </div>
        </div>
        <div v-show="showMode === 'default'">
            <div class="flex-col">
                <div class="w-full flex justify-center items-center text-2xl text-medium uppercase">Directed</div>
                <div class="flex">
                    <div class="flex flex-col gap-4">
                        <div id="directedMatrixTable" class="flex justify-center items-center">
                            <table class="table-fixed text-center">
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7"></td>
                                    <td v-for="(row, i) in directedGraphMatrix" :key="i" class="w-7 h-7">{{
                                            i + 1
                                        }}
                                    </td>
                                </tr>
                                <tr v-for="(row, i) in directedGraphMatrix" :key="i"
                                    class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7">{{ i + 1 }}</td>
                                    <td v-for="(el, j) in row" :key="j" class="w-7 h-7 text-xs">
                                        {{ el }}
                                    </td>
                                </tr>
                            </table>
                        </div>
                        <div id="directedCharacteristics" class="">
                            <table class="table-fixed text-center">
                                <tr class="bg-red-200 border-b-4">
                                    <td class="w-7 h-7 p-2">Вершини</td>
                                    <td v-for="i in peaksNumber" :key="i" class="w-7 h-7 p-2">{{ i }}</td>
                                </tr>
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7 p-2">Степені</td>
                                    <td v-for="i in directedPeaksPower" :key="i" class="w-7 h-7 p-2">{{ i }}</td>
                                </tr>
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7 p-2">Напівстепені виходу</td>
                                    <td v-for="power in directedPeaksHalfPowerOut" :key="power" class="w-7 h-7 p-2">
                                        {{ power }}
                                    </td>
                                </tr>
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7 p-2">Напівстепені заходу</td>
                                    <td v-for="i in directedPeaksHalfPowerIn" :key="i" class="w-7 h-7 p-2">{{ i }}</td>
                                </tr>
                            </table>
                        </div>
                        <div>
                            <div class="flex gap-3">
                                <div>Однорідність:</div>
                                <div v-if="directedRegularPower" class="flex flex-row gap-3">
                                    <div>+</div>
                                    <div>Степінь: ({{ directedRegularPower }})</div>
                                </div>
                                <div v-else>-</div>
                            </div>
                            <div class="flex gap-3">
                                <div>Висячі вершини:</div>
                                <div v-if="directedHangingPeaks.length" class="flex flex-row gap-3">
                                    <div>{{ directedHangingPeaks }}</div>
                                </div>
                                <div v-else>-</div>
                            </div>
                            <div class="flex gap-3">
                                <div>Ізольовані вершини:</div>
                                <div v-if="isolatedPeaks.length" class="flex flex-row gap-3">
                                    <div>{{ isolatedPeaks }}</div>
                                </div>
                                <div v-else>-</div>
                            </div>
                        </div>
                    </div>
                    <div id="directedGraph" class="w-full h-full flex justify-center items-center"></div>
                </div>
            </div>
            <div class="flex-col">
                <div class="w-full flex justify-center items-center text-2xl text-medium uppercase">Undirected</div>
                <div class="flex">
                    <div class="flex flex-col gap-4">
                        <div id="undirectedMatrixTable" class="flex justify-center items-center">
                            <table class="table-fixed text-center">
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7"></td>
                                    <td v-for="(row, i) in undirectedGraphMatrix" :key="i" class="w-7 h-7">{{
                                            i + 1
                                        }}
                                    </td>
                                </tr>
                                <tr v-for="(row, i) in undirectedGraphMatrix" :key="i"
                                    class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7">{{ i + 1 }}</td>
                                    <td v-for="(el, j) in row" :key="j" class="w-7 h-7 text-xs">
                                        {{ el }}
                                    </td>
                                </tr>
                            </table>
                        </div>
                        <div id="undirectedCharacteristics" class="">
                            <table class="table-fixed text-center">
                                <tr class="bg-red-200 border-b-4">
                                    <td class="w-7 h-7 p-2">Вершини</td>
                                    <td v-for="i in peaksNumber" :key="i" class="w-7 h-7 p-2">{{ i }}</td>
                                </tr>
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7 p-2">Степені</td>
                                    <td v-for="i in undirectedPeaksPower" :key="i" class="w-7 h-7 p-2">{{ i }}</td>
                                </tr>
                            </table>
                        </div>
                        <div>
                            <div class="flex gap-3">
                                <div>Однорідність:</div>
                                <div v-if="undirectedRegularPower" class="flex flex-row gap-3">
                                    <div>+</div>
                                    <div>Степінь: ({{ undirectedRegularPower }})</div>
                                </div>
                                <div v-else>-</div>
                            </div>
                            <div class="flex gap-3">
                                <div>Висячі вершини:</div>
                                <div v-if="undirectedHangingPeaks.length" class="flex flex-row gap-3">
                                    <div>{{ undirectedHangingPeaks }}</div>
                                </div>
                                <div v-else>-</div>
                            </div>
                            <div class="flex gap-3">
                                <div>Ізольовані вершини:</div>
                                <div v-if="isolatedPeaks.length" class="flex flex-row gap-3">
                                    <div class="whitespace-nowrap">{{ isolatedPeaks }}</div>
                                </div>
                                <div v-else>-</div>
                            </div>
                        </div>
                    </div>
                    <div id="undirectedGraph" class="w-full h-full flex justify-center items-center"></div>
                </div>
            </div>
        </div>
        <div v-show="showMode === 'changed'">
            <div class="flex-col">
                <div class="w-full flex justify-center items-center text-2xl text-medium uppercase">Changed Directed
                </div>
                <div class="flex">
                    <div class="flex flex-col gap-4">
                        <div id="directedMatrixTableChanged" class="flex justify-center items-center">
                            <table class="table-fixed text-center">
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7"></td>
                                    <td v-for="(row, i) in changedDirectedGraphMatrix" :key="i" class="w-7 h-7">{{
                                            i + 1
                                        }}
                                    </td>
                                </tr>
                                <tr v-for="(row, i) in changedDirectedGraphMatrix" :key="i"
                                    class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7">{{ i + 1 }}</td>
                                    <td v-for="(el, j) in row" :key="j" class="w-7 h-7 text-xs">
                                        {{ el }}
                                    </td>
                                </tr>
                            </table>
                        </div>
                        <div id="directedCharacteristicsChanged" class="flex justify-center items-center">
                            <table class="table-fixed text-center">
                                <tr class="bg-red-200 border-b-4">
                                    <td class="w-7 h-7 p-2">Вершини</td>
                                    <td v-for="i in peaksNumber" :key="i" class="w-7 h-7 p-2">{{ i }}</td>
                                </tr>
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7 p-2">Напівстепені виходу</td>
                                    <td v-for="power in changedDirectedPeaksHalfPowerOut" :key="power"
                                        class="w-7 h-7 p-2">
                                        {{ power }}
                                    </td>
                                </tr>
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-7 h-7 p-2">Напівстепені заходу</td>
                                    <td v-for="i in changedDirectedPeaksHalfPowerIn" :key="i" class="w-7 h-7 p-2">
                                        {{ i }}
                                    </td>
                                </tr>
                            </table>
                        </div>
                    </div>
                    <div id="changedDirectedGraph" class="w-full h-full flex justify-center items-center"></div>
                </div>
                <div class="flex flex-row gap-5">
                    <div>
                        <div>
                            Матриця досяжності
                        </div>
                        <table class="table-fixed text-center">
                            <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                <td class="w-7 h-7"></td>
                                <td v-for="(row, i) in changedDirectedReachabilityMatrix" :key="i" class="w-7 h-7">{{
                                        i + 1
                                    }}
                                </td>
                            </tr>
                            <tr v-for="(row, i) in changedDirectedReachabilityMatrix" :key="i"
                                class="odd:bg-white even:bg-gray-100 border-b-4">
                                <td class="w-7 h-7">{{ i + 1 }}</td>
                                <td v-for="(el, j) in row" :key="j" class="w-7 h-7 text-xs">
                                    {{ el }}
                                </td>
                            </tr>
                        </table>
                    </div>
                    <div>
                        <div>
                            Матриця cильної звʼязності
                        </div>
                        <table class="table-fixed text-center">
                            <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                <td class="w-7 h-7"></td>
                                <td v-for="(row, i) in changedDirectedStrongConnectivityMatrix" :key="i"
                                    class="w-7 h-7">{{
                                        i + 1
                                    }}
                                </td>
                            </tr>
                            <tr v-for="(row, i) in changedDirectedStrongConnectivityMatrix" :key="i"
                                class="odd:bg-white even:bg-gray-100 border-b-4">
                                <td class="w-7 h-7">{{ i + 1 }}</td>
                                <td v-for="(el, j) in row" :key="j" class="w-7 h-7 text-xs">
                                    {{ el }}
                                </td>
                            </tr>
                        </table>
                    </div>
                    <div>
                        <div>
                            Компоненти сильної звʼязності:
                        </div>
                        <div v-for="component in changedDirectedStrongComponents" :key="component">
                            {{ component }}
                        </div>
                    </div>
                    <div>
                        <div>
                            Матриця Конденсації:
                        </div>
                        <table class="table-fixed text-center">
                            <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                <td class="w-7 h-7"></td>
                                <td v-for="(row, i) in changedDirectedCondensationMatrix" :key="i" class="w-7 h-7">{{
                                        i + 1
                                    }}
                                </td>
                            </tr>
                            <tr v-for="(row, i) in changedDirectedCondensationMatrix" :key="i"
                                class="odd:bg-white even:bg-gray-100 border-b-4">
                                <td class="w-7 h-7">{{ i + 1 }}</td>
                                <td v-for="(el, j) in row" :key="j" class="w-7 h-7 text-xs">
                                    {{ el }}
                                </td>
                            </tr>
                        </table>

                    </div>
                </div>
                <div class="w-full flex justify-center items-center text-2xl text-medium uppercase mt-5">Condensation
                    Graph
                </div>
                <div class="flex justify-center items-center">
                    <div id="changedDirectedCondensationGraph"
                         class="w-1/2 h-1/2 flex justify-center items-center"></div>
                </div>
                <div class="">
                    <div class="mb-4">
                        <div>
                            Шляхи довжиною 2
                        </div>
                        <div class="grid grid-rows-12 grid-flow-col gap-4 border border-black rounded-md p-3">

                            <div class="whitespace-nowrap" v-for="path in changedDirectedPaths2" :key="path.join('-')">
                                {{ path.join('-') }}
                            </div>
                        </div>
                    </div>
                    <div>
                        <div>
                            Шляхи довжиною 3
                        </div>
                        <div class="grid grid-rows-30 grid-flow-col gap-4 border border-black rounded-md p-3">
                            <div class="whitespace-nowrap" v-for="path in changedDirectedPaths3" :key="path.join('-')">
                                {{ path.join('-') }}
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>

</template>

<script>
import {SVG} from '@svgdotjs/svg.js'

export default {
    // eslint-disable-next-line vue/multi-word-component-names
    name: 'HelloWorld',
    props: {
        msg: String
    },
    data: () => {
        return {
            n1: null,
            n2: null,
            n3: null,
            n4: null,
            peaksNumber: null,
            windowWidth: null,
            windowHeight: null,
            windowCenterX: null,
            windowCenterY: null,
            peakDiameter: null,
            graphRadius: null,
            fontSize: null,
            ptSize: null,
            arrowBranchLength: null,
            directedGraphMatrix: [],
            undirectedGraphMatrix: [],
            peaksCoordinates: [],
            directedPeaksHalfPowerOut: [],
            directedPeaksHalfPowerIn: [],
            directedPeaksPower: [],
            undirectedPeaksPower: [],
            directedRegularPower: null,
            undirectedRegularPower: null,
            directedHangingPeaks: [],
            undirectedHangingPeaks: [],
            isolatedPeaks: [],
            showMode: 'default',
            changedDirectedGraphMatrix: [],
            changedDirectedPeaksHalfPowerOut: [],
            changedDirectedPeaksHalfPowerIn: [],
            changedDirectedPaths2: [],
            changedDirectedPaths3: [],
            changedDirectedReachabilityMatrix: [],
            changedDirectedStrongConnectivityMatrix: [],
            changedDirectedStrongComponents: [],
            changedDirectedCondensationMatrix: [],
        };
    },
    mounted() {
        this.calculate()
        this.drawDirectedGraph()
        this.drawUndirectedGraph()
        this.drawChangedDirectedGraph()
        this.drawChangedDirectedCondensationGraph()
    },
    methods: {
        setDirectedMatrix() {
            const k = 1 - this.n3 * 0.01 - this.n4 * 0.01 - 0.3;
            let seedrandom = require('seedrandom');
            let random = seedrandom(parseInt(`${this.n1}${this.n2}${this.n3}${this.n4}`));

            const directedMatrix = [];
            for (let i = 0; i < this.peaksNumber; i++) {
                directedMatrix[i] = [];
                for (let j = 0; j < this.peaksNumber; j++) {
                    directedMatrix[i][j] = (random() * 2.0 * k) < 1 ? 0 : 1;
                    // directedMatrix[i][j] = (Math.random() * 2.0 * k) < 1 ? 0 : 1;
                }
            }
            this.directedGraphMatrix = directedMatrix;
        },

        setUndirectedMatrix(matrix) {
            const undirectedMatrix = JSON.parse(JSON.stringify(matrix));

            for (let i = 0; i < matrix.length; i++) {
                for (let j = 0; j < matrix.length; j++) {
                    if (undirectedMatrix[i][j] === 1) {
                        undirectedMatrix[j][i] = 1;
                    }
                }
            }
            this.undirectedGraphMatrix = undirectedMatrix;
        },

        setChangedDirectedMatrix() {
            const k = 1 - this.n3 * 0.005 - this.n4 * 0.005 - 0.27;
            let seedrandom = require('seedrandom');
            let random = seedrandom(parseInt(`${this.n1}${this.n2}${this.n3}${this.n4}`));

            const changedDirectedMatrix = [];
            for (let i = 0; i < this.peaksNumber; i++) {
                changedDirectedMatrix[i] = [];
                for (let j = 0; j < this.peaksNumber; j++) {
                    changedDirectedMatrix[i][j] = (random() * 2.0 * k) < 1 ? 0 : 1;
                    // changedDirectedMatrix[i][j] = (Math.random() * 2.0 * k) < 1 ? 0 : 1;
                }
            }
            this.changedDirectedGraphMatrix = changedDirectedMatrix;
        },

        getPeaksCoordinates(peaksNumber, isCondensation = false) {
            let peaksCoordinates = [];
            let angle = 0;
            if (isCondensation) {
                peaksNumber += 1
            }

            for (let i = 1; i <= peaksNumber - 1; i++) {
                peaksCoordinates.push({
                    cx: this.windowCenterX - (this.graphRadius * Math.cos(angle)),
                    cy: this.windowCenterY - (this.graphRadius * Math.sin(angle)),
                    title: i.toString()
                });
                angle += 2 * Math.PI / (peaksNumber - 1);
            }

            if (!isCondensation) {
                peaksCoordinates.push({cx: this.windowCenterX, cy: this.windowCenterY, title: `${peaksNumber}`});
            }


            return peaksCoordinates;
        },

        drawPeaks(peaksCoordinates, draw) {
            for (const peakCoordinates of peaksCoordinates) {
                draw.circle(this.peakDiameter).center(peakCoordinates.cx, peakCoordinates.cy).fill('none').stroke({
                    width: 1,
                    color: '#000'
                });
                draw.text(peakCoordinates.title).amove(peakCoordinates.cx, peakCoordinates.cy + (this.fontSize * this.ptSize)).font({
                    anchor: 'middle',
                    size: this.fontSize
                });
            }
        },

        drawDirectedLine(start, end, drawDirected, isCurved = false) {
            let peakOffsetStartX = 0, peakOffsetStartY = 0;
            let peakOffsetEndX = 0, peakOffsetEndY = 0;
            let isControlPointReversed = false;


            const originalStart = start;
            if (start.cy < end.cy) {
                const tmp = start;
                start = end;
                end = tmp;
                isControlPointReversed = true;
            }


            const directedVector = {
                x: end.cx - start.cx,
                y: end.cy - start.cy
            };

            const directedVectorLength = Math.sqrt(directedVector.x * directedVector.x + directedVector.y * directedVector.y);

            let angleStart = Math.acos(directedVector.x / directedVectorLength);
            let angleEnd = Math.PI / 2 - angleStart;

            peakOffsetEndX = this.peakDiameter / 2 * Math.cos(angleStart);
            peakOffsetEndY = this.peakDiameter / 2 * Math.sin(angleStart);

            peakOffsetStartX = this.peakDiameter / 2 * Math.sin(angleEnd);
            peakOffsetStartY = this.peakDiameter / 2 * Math.cos(angleEnd);


            const startPoint = {
                x: start.cx + peakOffsetStartX,
                y: start.cy - peakOffsetStartY
            };
            const endPoint = {
                x: end.cx - peakOffsetEndX,
                y: end.cy + peakOffsetEndY
            };


            // let color = `rgb(${Math.floor(Math.random() * 255)}, ${Math.floor(Math.random() * 255)}, ${Math.floor(Math.random() * 255)})`;
            let color = isCurved ? 'orange' : 'black'

            let controlPoint1;
            let correlation = this.arrowBranchLength / directedVectorLength;

            if (!isCurved) {
                controlPoint1 = {
                    x: isControlPointReversed ? endPoint.x : startPoint.x,
                    y: isControlPointReversed ? endPoint.y : startPoint.y
                };

                drawDirected.line(startPoint.x, startPoint.y, endPoint.x, endPoint.y).stroke({color: color, width: 1});
            } else {
                correlation *= 2;
                controlPoint1 = {x: (endPoint.x + startPoint.x) / 2 - 20, y: (endPoint.y + startPoint.y) / 2 - 20};

                drawDirected.path(`M ${startPoint.x} ${startPoint.y} Q ${controlPoint1.x} ${controlPoint1.y}, ${endPoint.x} ${endPoint.y}`).fill('none').stroke({
                    width: 1,
                    color: color
                });
            }
            if (start !== originalStart) {
                const arrowOffsetStart = {
                    x: (startPoint.x + correlation * controlPoint1.x) / (1 + correlation),
                    y: (startPoint.y + correlation * controlPoint1.y) / (1 + correlation)
                };
                drawDirected.line(startPoint.x, startPoint.y, arrowOffsetStart.x, arrowOffsetStart.y).stroke({
                    color: color,
                    width: 1
                }).rotate(20, startPoint.x, startPoint.y);
                drawDirected.line(startPoint.x, startPoint.y, arrowOffsetStart.x, arrowOffsetStart.y).stroke({
                    color: color,
                    width: 1
                }).rotate(-20, startPoint.x, startPoint.y);
            } else {
                const arrowOffsetEnd = {
                    x: (endPoint.x + correlation * controlPoint1.x) / (1 + correlation),
                    y: (endPoint.y + correlation * controlPoint1.y) / (1 + correlation)
                };
                drawDirected.line(endPoint.x, endPoint.y, arrowOffsetEnd.x, arrowOffsetEnd.y).stroke({
                    color: color,
                    width: 1
                }).rotate(20, endPoint.x, endPoint.y);
                drawDirected.line(endPoint.x, endPoint.y, arrowOffsetEnd.x, arrowOffsetEnd.y).stroke({
                    color: color,
                    width: 1
                }).rotate(-20, endPoint.x, endPoint.y);
            }
        },

        drawCurveBezier(peakIndex, draw, isDirected = false) {
            const angle = Math.PI / 4;

            const startPoint = {
                x: peakIndex.cx - ((this.peakDiameter / 2) * Math.sin(angle)),
                y: peakIndex.cy + ((this.peakDiameter / 2) * Math.cos(angle))
            };
            const endPoint = {
                x: peakIndex.cx - ((this.peakDiameter / 2) * Math.sin(angle)),
                y: peakIndex.cy - ((this.peakDiameter / 2) * Math.cos(angle))
            };
            const controlPoint1 = {x: startPoint.x - this.peakDiameter, y: startPoint.y + this.peakDiameter / 2};
            const controlPoint2 = {x: endPoint.x - this.peakDiameter, y: endPoint.y - this.peakDiameter / 2};

            draw.path(`M ${startPoint.x} ${startPoint.y} C ${controlPoint1.x} ${controlPoint1.y} ${controlPoint2.x} ${controlPoint2.y}, ${endPoint.x} ${endPoint.y}`).fill('none').stroke({
                width: 1,
                color: 'red'
            });

            if (isDirected) {
                const correlation = 1 / 4;

                const arrowOffsetEnd = {
                    x: (endPoint.x + correlation * controlPoint2.x) / (1 + correlation),
                    y: (endPoint.y + correlation * controlPoint2.y) / (1 + correlation)
                };
                draw.line(endPoint.x, endPoint.y, arrowOffsetEnd.x, arrowOffsetEnd.y).stroke({
                    color: 'red',
                    width: 1
                }).rotate(20, endPoint.x, endPoint.y);
                draw.line(endPoint.x, endPoint.y, arrowOffsetEnd.x, arrowOffsetEnd.y).stroke({
                    color: 'red',
                    width: 1
                }).rotate(-20, endPoint.x, endPoint.y);
            }
        },

        drawUndirectedLine(start, end, drawUndirected) {
            let peakOffsetStartX = 0, peakOffsetStartY = 0;
            let peakOffsetEndX = 0, peakOffsetEndY = 0;

            if (start.cy < end.cy) {
                const tmp = start;
                start = end;
                end = tmp;
            }

            const directedVector = {
                x: end.cx - start.cx,
                y: end.cy - start.cy
            };

            const directedVectorLength = Math.sqrt(directedVector.x * directedVector.x + directedVector.y * directedVector.y);

            let angleStart = Math.acos(directedVector.x / directedVectorLength);
            let angleEnd = Math.PI / 2 - angleStart;

            peakOffsetEndX = this.peakDiameter / 2 * Math.cos(angleStart);
            peakOffsetEndY = this.peakDiameter / 2 * Math.sin(angleStart);

            peakOffsetStartX = this.peakDiameter / 2 * Math.sin(angleEnd);
            peakOffsetStartY = this.peakDiameter / 2 * Math.cos(angleEnd);


            const startPoint = {
                x: start.cx + peakOffsetStartX,
                y: start.cy - peakOffsetStartY
            };
            const endPoint = {
                x: end.cx - peakOffsetEndX,
                y: end.cy + peakOffsetEndY
            };

            drawUndirected.line(startPoint.x, startPoint.y, endPoint.x, endPoint.y).stroke({color: '#000', width: 1});
        },

        drawDirectedGraph() {
            let drawDirected = SVG().addTo('#directedGraph').size(this.windowWidth, this.windowWidth);

            this.drawPeaks(this.peaksCoordinates, drawDirected);

            for (let i = 0; i < this.directedGraphMatrix.length; i++) {
                for (let j = 0; j < this.directedGraphMatrix.length; j++) {
                    if (this.directedGraphMatrix[i][j] === 1) {
                        const start = {
                            cx: this.peaksCoordinates[i].cx,
                            cy: this.peaksCoordinates[i].cy
                        }
                        const end = {
                            cx: this.peaksCoordinates[j].cx,
                            cy: this.peaksCoordinates[j].cy
                        }
                        if (i !== j) {
                            if (this.directedGraphMatrix[i][j] === this.directedGraphMatrix[j][i] && i < j) {
                                this.drawDirectedLine(start, end, drawDirected, false);
                                this.drawDirectedLine(end, start, drawDirected, true);
                            } else if (this.directedGraphMatrix[i][j] !== this.directedGraphMatrix[j][i]) {
                                this.drawDirectedLine(start, end, drawDirected, false);
                            }
                        } else {
                            this.drawCurveBezier(start, drawDirected, true);
                        }
                    }
                }
            }
        },

        drawUndirectedGraph() {
            let drawUndirected = SVG().addTo('#undirectedGraph').size(this.windowWidth, this.windowWidth);

            this.drawPeaks(this.peaksCoordinates, drawUndirected);

            for (let i = 0; i < this.undirectedGraphMatrix.length; i++) {
                for (let j = 0; j < this.undirectedGraphMatrix.length; j++) {
                    const start = {
                        cx: this.peaksCoordinates[i].cx,
                        cy: this.peaksCoordinates[i].cy
                    }
                    const end = {
                        cx: this.peaksCoordinates[j].cx,
                        cy: this.peaksCoordinates[j].cy
                    }
                    if (this.undirectedGraphMatrix[i][j] === 1) {
                        if (i !== j) {
                            this.drawUndirectedLine(start, end, drawUndirected);
                        } else {
                            this.drawCurveBezier(start, drawUndirected);
                        }
                    }
                }
            }
        },

        drawChangedDirectedGraph() {
            let drawChangedDirected = SVG().addTo('#changedDirectedGraph').size(this.windowWidth, this.windowWidth);

            this.drawPeaks(this.peaksCoordinates, drawChangedDirected);

            for (let i = 0; i < this.changedDirectedGraphMatrix.length; i++) {
                for (let j = 0; j < this.changedDirectedGraphMatrix.length; j++) {
                    if (this.changedDirectedGraphMatrix[i][j] === 1) {
                        const start = {
                            cx: this.peaksCoordinates[i].cx,
                            cy: this.peaksCoordinates[i].cy
                        }
                        const end = {
                            cx: this.peaksCoordinates[j].cx,
                            cy: this.peaksCoordinates[j].cy
                        }
                        if (i !== j) {
                            if (this.changedDirectedGraphMatrix[i][j] === this.changedDirectedGraphMatrix[j][i] && i < j) {
                                this.drawDirectedLine(start, end, drawChangedDirected, false);
                                this.drawDirectedLine(end, start, drawChangedDirected, true);
                            } else if (this.changedDirectedGraphMatrix[i][j] !== this.changedDirectedGraphMatrix[j][i]) {
                                this.drawDirectedLine(start, end, drawChangedDirected, false);
                            }
                        } else {
                            this.drawCurveBezier(start, drawChangedDirected, true);
                        }
                    }
                }
            }
        },

        drawChangedDirectedCondensationGraph() {
            let drawChangedDirectedCondensation = SVG().addTo('#changedDirectedCondensationGraph').size(this.windowWidth, this.windowWidth);

            const peaksCoordinates = this.getPeaksCoordinates(this.changedDirectedCondensationMatrix.length, true)
            this.drawPeaks(peaksCoordinates, drawChangedDirectedCondensation);

            for (let i = 0; i < this.changedDirectedCondensationMatrix.length; i++) {
                for (let j = 0; j < this.changedDirectedCondensationMatrix.length; j++) {
                    if (this.changedDirectedCondensationMatrix[i][j] === 1) {
                        const start = {
                            cx: peaksCoordinates[i].cx,
                            cy: peaksCoordinates[i].cy
                        }
                        const end = {
                            cx: peaksCoordinates[j].cx,
                            cy: peaksCoordinates[j].cy
                        }
                        if (i !== j) {
                            if (this.changedDirectedCondensationMatrix[i][j] === this.changedDirectedCondensationMatrix[j][i] && i < j) {
                                this.drawDirectedLine(start, end, drawChangedDirectedCondensation, false);
                                this.drawDirectedLine(end, start, drawChangedDirectedCondensation, true);
                            } else if (this.changedDirectedCondensationMatrix[i][j] !== this.changedDirectedCondensationMatrix[j][i]) {
                                this.drawDirectedLine(start, end, drawChangedDirectedCondensation, false);
                            }
                        } else {
                            this.drawCurveBezier(start, drawChangedDirectedCondensation, true);
                        }
                    }
                }
            }
        },

        getPeaksHalfPowerOut(matrix) {
            let i = 0;
            const peaksHalfPowerOut = [];
            for (const row of matrix) {
                peaksHalfPowerOut[i] = row.reduce((a, b) => a + b, 0);
                i++;
            }
            return peaksHalfPowerOut;
        },

        getPeaksHalfPowerIn(matrix) {
            const peaksHalfPowerIn = [];
            for (let i = 0; i <= this.peaksNumber - 1; i++) {
                peaksHalfPowerIn[i] = 0
                for (let j = 0; j <= this.peaksNumber - 1; j++) {
                    peaksHalfPowerIn[i] += matrix[j][i]
                }
            }
            return peaksHalfPowerIn;
        },

        setDirectedPeaksPower() {
            for (let i = 0; i <= this.peaksNumber - 1; i++) {
                this.directedPeaksPower[i] = this.directedPeaksHalfPowerOut[i] + this.directedPeaksHalfPowerIn[i]
            }
        },

        setUndirectedPeaksPower() {
            let i = 0;
            for (const row of this.undirectedGraphMatrix) {
                this.undirectedPeaksPower[i] = row.reduce((a, b) => a + b, 0);
                if (row[i] === 1) {
                    this.undirectedPeaksPower[i] += 1
                }
                i++;
            }
        },

        setDirectedRegularPower() {
            const powerSet = new Set(this.directedPeaksPower)

            if (powerSet.size === 1) {
                this.directedRegularPower = powerSet.values().next().value
            }
        },

        setUndirectedRegularPower() {
            const powerSet = new Set(this.undirectedPeaksPower)
            if (powerSet.size === 1) {
                this.undirectedRegularPower = powerSet.values().next().value
            }
        },

        setDirectedHangingPeaks() {
            this.directedHangingPeaks = []
            for (let i = 0; i <= this.peaksNumber - 1; i++) {
                if (this.directedPeaksPower[i] === 1) {
                    this.directedHangingPeaks.push(i + 1)
                }
            }
        },

        setUndirectedHangingPeaks() {
            this.undirectedHangingPeaks = []
            for (let i = 0; i <= this.peaksNumber - 1; i++) {
                if (this.undirectedPeaksPower[i] === 1) {
                    this.undirectedHangingPeaks.push(i + 1)
                }
            }
        },

        setIsolatedPeaks() {
            this.isolatedPeaks = []
            for (let i = 0; i <= this.peaksNumber - 1; i++) {
                if (this.directedPeaksPower[i] === 0) {
                    this.isolatedPeaks.push(i + 1)
                }
            }
        },

        toggleShowMode() {
            if (this.showMode === 'default') {
                this.showMode = 'changed'
            } else {
                this.showMode = 'default'
            }
        },

        findPaths2(matrix) {
            const n = matrix.length;
            const A2 = this.multiplyMatrix(matrix, matrix);
            const changedDirectedPaths2 = [];

            for (let i = 0; i < n; i++) {
                for (let j = 0; j < n; j++) {
                    if (A2[i][j]) {
                        for (let k = 0; k < n; k++) {
                            if (matrix[k][j] && matrix[i][k]) {
                                changedDirectedPaths2.push([i + 1, k + 1, j + 1]);
                            }
                        }

                    }
                }
            }

            this.changedDirectedPaths2 = changedDirectedPaths2;
        },


        findPaths3(matrix) {
            const n = matrix.length;
            const A2 = this.multiplyMatrix(matrix, matrix);
            const A3 = this.multiplyMatrix(matrix, A2);
            const changedDirectedPaths3 = [];

            for (let i = 0; i < n; i++) {
                for (let j = 0; j < n; j++) {
                    if (A3[i][j]) {
                        for (let k = 0; k < n; k++) {
                            for (let x = 0; x < n; x++) {
                                if (matrix[i][k] && matrix[k][x] && matrix[x][j]) {
                                    changedDirectedPaths3.push([i + 1, k + 1, x + 1, j + 1]);
                                }
                            }
                        }
                    }
                }
            }

            this.changedDirectedPaths3 = changedDirectedPaths3;
        },

        multiplyMatrix(matrix1, matrix2) {
            const result = [];
            const n = matrix1.length;

            for (let i = 0; i < n; i++) {
                result[i] = [];
                for (let j = 0; j < n; j++) {
                    let sum = 0;
                    for (let k = 0; k < n; k++) {
                        sum += matrix1[i][k] * matrix2[k][j];
                    }
                    result[i][j] = sum;
                }
            }

            return result;
        },

        sumMatrix(matrix1, matrix2) {
            const resultMatrix = [];

            for (let i = 0; i < matrix1.length; i++) {
                resultMatrix[i] = [];
                for (let j = 0; j < matrix1[0].length; j++) {
                    resultMatrix[i][j] = matrix1[i][j] + matrix2[i][j];
                }
            }

            return resultMatrix
        },

        transposeMatrix(matrix) {
            const transposedMatrix = [];

            for (let i = 0; i < matrix[0].length; i++) {
                transposedMatrix[i] = []
                for (let j = 0; j < matrix.length; j++) {
                    transposedMatrix[i][j] = matrix[j][i];
                }
            }

            return transposedMatrix;
        },

        multiplyMatrixByElement(matrix1, matrix2) {
            const multipliedMatrixByElement = []

            for (let i = 0; i < matrix1.length; i++) {
                multipliedMatrixByElement[i] = []
                for (let j = 0; j < matrix2[0].length; j++) {
                    multipliedMatrixByElement[i][j] = matrix1[i][j] * matrix2[i][j];
                }
            }

            return multipliedMatrixByElement;
        },

        getReachabilityMatrix(matrix) {
            const n = matrix.length;
            const identityMatrix = Array.from({length: n}, (_, i) => Array.from({length: n}, (_, j) => i === j ? 1 : 0));
            let matrixPowers = matrix
            let reachabilityMatrix = this.sumMatrix(identityMatrix, matrixPowers);

            for (let i = 0; i < n - 2; i++) {
                matrixPowers = this.multiplyMatrix(matrixPowers, matrix);
                reachabilityMatrix = this.sumMatrix(reachabilityMatrix, matrixPowers);
            }

            for (let i = 0; i < n; i++) {
                for (let j = 0; j < n; j++) {
                    reachabilityMatrix[i][j] = reachabilityMatrix[i][j] > 0 ? 1 : 0;
                }
            }

            return reachabilityMatrix;
        },

        getStrongConnectivityMatrix(reachabilityMatrix) {
            const transposedReachabilityMatrix = this.transposeMatrix(reachabilityMatrix);

            return this.multiplyMatrixByElement(reachabilityMatrix, transposedReachabilityMatrix)
        },

        getStrongComponents(strongConnectivityMatrix) {
            const n = strongConnectivityMatrix.length;
            const visited = new Array(n).fill(false);
            const strongComponents = []


            const depthFirstSearch = (peak, component) => {
                visited[peak] = true;
                component.push(peak + 1);

                for (let i = 0; i < n; i++) {
                    if (strongConnectivityMatrix[peak][i] && !visited[i]) {
                        depthFirstSearch(i, component);
                    }
                }
            };

            for (let i = 0; i < n; i++) {
                if (!visited[i]) {
                    const component = [];
                    depthFirstSearch(i, component);
                    strongComponents.push(component);
                }
            }

            return strongComponents;
        },

        getCondensationMatrix(graphMatrix, components) {
            const condensationMatrixLength = components.length
            const condensationMatrix = []

            for (let i = 0; i < condensationMatrixLength; i++) {
                condensationMatrix.push(new Array(condensationMatrixLength).fill(0))
            }

            for (let i = 0; i < condensationMatrixLength; i++) {
                for (let j = 0; j < condensationMatrixLength; j++) {
                    if (i === j) continue;
                    let isConnected = false;
                    for (let x = 0; x < components[i].length; x++) {
                        for (let k = 0; k < components[j].length; k++) {
                            if (graphMatrix[components[i][x] - 1][components[j][k] - 1]) {
                                condensationMatrix[i][j] = 1;
                                isConnected = true;
                                break;
                            }
                        }
                        if (isConnected) {
                            break
                        }
                    }
                }
            }

            return condensationMatrix;
        },

        setConstData() {
            this.n1 = 3;
            this.n2 = 2;
            const n3 = 0
            this.n3 = n3;
            this.n4 = 7;
            this.peaksNumber = n3 + 10;

            const windowWidth = window.innerWidth / 2;
            this.windowWidth = windowWidth;
            const windowHeight = window.innerWidth / 2;
            this.windowHeight = windowHeight / 2;

            this.windowCenterX = windowWidth / 2;
            this.windowCenterY = windowHeight / 2;

            const peakDiameter = 50;
            this.peakDiameter = peakDiameter;
            this.graphRadius = peakDiameter * 5;
            this.fontSize = 20;
            this.ptSize = 0.376;
            this.arrowBranchLength = 15;
        },

        calculate() {
            this.setConstData()
            this.setDirectedMatrix()
            this.setUndirectedMatrix(this.directedGraphMatrix)
            this.peaksCoordinates = this.getPeaksCoordinates(this.peaksNumber)
            this.directedPeaksHalfPowerOut = this.getPeaksHalfPowerOut(this.directedGraphMatrix)
            this.directedPeaksHalfPowerIn = this.getPeaksHalfPowerIn(this.directedGraphMatrix)
            this.setDirectedPeaksPower()
            this.setUndirectedPeaksPower()
            this.setDirectedRegularPower()
            this.setUndirectedRegularPower()
            this.setDirectedHangingPeaks()
            this.setUndirectedHangingPeaks()
            this.setIsolatedPeaks()
            this.setChangedDirectedMatrix()
            this.changedDirectedPeaksHalfPowerOut = this.getPeaksHalfPowerOut(this.changedDirectedGraphMatrix)
            this.changedDirectedPeaksHalfPowerIn = this.getPeaksHalfPowerIn(this.changedDirectedGraphMatrix)
            this.findPaths2(this.changedDirectedGraphMatrix)
            this.findPaths3(this.changedDirectedGraphMatrix)
            this.changedDirectedReachabilityMatrix = this.getReachabilityMatrix(this.changedDirectedGraphMatrix)
            this.changedDirectedStrongConnectivityMatrix = this.getStrongConnectivityMatrix(this.changedDirectedReachabilityMatrix)
            this.changedDirectedStrongComponents = this.getStrongComponents(this.changedDirectedStrongConnectivityMatrix)
            this.changedDirectedCondensationMatrix = this.getCondensationMatrix(this.changedDirectedGraphMatrix, this.changedDirectedStrongComponents)
        }
    }
}
</script>
