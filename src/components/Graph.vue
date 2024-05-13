<template>
    <div class="p-5">
        <div>
            <div class="flex-col">
                <div class="w-full flex justify-center items-center text-2xl text-medium uppercase">el prima algorithm
                </div>
                <div class="flex">
                    <div class="flex flex-col gap-4 justify-center items-center">
                        <div id="directedMatrixTable" class="flex justify-center items-center">
                            <table class="table-fixed text-center">
                                <tr class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-10 h-10"></td>
                                    <td v-for="(row, i) in wMatrix" :key="i" class="w-10 h-10">{{
                                            i + 1
                                        }}
                                    </td>
                                </tr>
                                <tr v-for="(row, i) in wMatrix" :key="i"
                                    class="odd:bg-white even:bg-gray-100 border-b-4">
                                    <td class="w-10 h-10">{{ i + 1 }}</td>
                                    <td v-for="(el, j) in row" :key="j" class="w-10 h-10 text-xs">
                                        {{ el }}
                                    </td>
                                </tr>
                            </table>
                        </div>
                        <div class="flex rounded-md " role="group">
                            <button @click="primaDoReset" type="button"
                                    class="px-4 py-2 text-sm font-medium text-gray-900 bg-white border border-gray-200 rounded-s-lg hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700">
                                Reset
                            </button>
                            <button :class="{'cursor-not-allowed opacity-50': this.stepNumber === this.peaksNumber - 1}"
                                    :disabled="this.stepNumber === this.peaksNumber"
                                    @click="primaDoNextStep"
                                    type="button"
                                    class="px-4 py-2 text-sm font-medium text-gray-900 bg-white border border-gray-200 rounded-e-lg hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700">
                                Next Step
                            </button>
                        </div>
                        <div class="">
                            Prima steps: {{ this.primaStepsToDisplay.slice(0, -1) }}
                        </div>
                        <div class="whitespace-nowrap">
                            Weight sum: {{ this.weightSum }}
                        </div>
                    </div>
                    <div id="undirectedGraph" class="w-full h-full flex justify-center items-center"></div>
                </div>
            </div>

        </div>
    </div>

</template>

<script>
import {SVG} from '@svgdotjs/svg.js'

class Node {
    constructor(data) {
        this.data = data;
        this.next = null;
    }
}

class LinkedList {
    constructor() {
        this.head = null;
    }

    add(data) {
        const newNode = new Node(data);
        if (!this.head) {
            this.head = newNode;
        } else {
            let current = this.head;
            while (current.next) {
                current = current.next;
            }
            current.next = newNode;
        }
    }

    getData() {
        const result = [];
        let current = this.head;
        while (current) {
            result.push({
                data: current.data,
            });
            current = current.next;
        }
        return result;
    }

    getLast() {
        if (!this.head) {
            return null;
        }
        let current = this.head;
        while (current.next) {
            current = current.next;
        }
        return current;
    }

    getStepsToDisplay() {
        const steps = [];
        let current = this.head;
        while (current) {
            steps.push([current.data.step[0] + 1, current.data.step[1] + 1, current.data.step[2]]);
            current = current.next;
        }
        return steps;
    }

    getWeightSum() {
        let sum = 0;
        let current = this.head;
        while (current) {
            sum += current.data.step[2]
            current = current.next;
        }
        return sum
    }
}


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
            bMatrix: [],
            cMatrix: [],
            dMatrix: [],
            hMatrix: [],
            tMatrix: [],
            wMatrix: [],
            peaksCoordinates: [],
            showMode: 'bfs',
            BFSPaths: [],
            BFSSteps: [],
            BFSStep: 0,
            BFSVector: [],
            drawDirectedBFS: null,
            DFSPaths: [],
            DFSSteps: [],
            DFSStep: 0,
            DFSVector: [],
            drawDirectedDFS: null,
            primaSteps: null,
            primaStep: null,
            drawUndirected: null,
            stepNumber: 0,
            primaStepsToDisplay: [],
            weightSum: null,

        };
    },
    mounted() {
        this.calculate()
        this.drawUndirectedGraph()
        this.prima()
        this.setWeightSum()
    },
    methods: {
        setDirectedMatrix() {
            const k = 1 - this.n3 * 0.01 - this.n4 * 0.005 - 0.05;
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
                this.drawPeak(peakCoordinates, draw, '#000');
            }
        },

        drawPeak(peakCoordinate, draw, color, width) {
            draw.circle(this.peakDiameter).center(peakCoordinate.cx, peakCoordinate.cy).fill('none').stroke({
                width: width,
                color: color
            });
            draw.text(peakCoordinate.title).amove(peakCoordinate.cx, peakCoordinate.cy + (this.fontSize * this.ptSize)).font({
                anchor: 'middle',
                size: this.fontSize
            });
        },

        drawDirectedLine(start, end, drawDirected, color, width, isCurved = false) {
            let peakOffsetStartX = 0, peakOffsetStartY = 0;
            let peakOffsetEndX = 0, peakOffsetEndY = 0;
            let isControlPointReversed = false;

            let straightLine, arrowBranch1, arrowBranch2;


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


            let controlPoint1;
            let correlation = this.arrowBranchLength / directedVectorLength;

            if (!isCurved) {
                controlPoint1 = {
                    x: isControlPointReversed ? endPoint.x : startPoint.x,
                    y: isControlPointReversed ? endPoint.y : startPoint.y
                };

                straightLine = drawDirected.line(startPoint.x, startPoint.y, endPoint.x, endPoint.y).stroke({
                    color: color,
                    width: width
                })
            } else {
                correlation *= 2;
                controlPoint1 = {x: (endPoint.x + startPoint.x) / 2 - 20, y: (endPoint.y + startPoint.y) / 2 - 20};

                drawDirected.path(`M ${startPoint.x} ${startPoint.y} Q ${controlPoint1.x} ${controlPoint1.y}, ${endPoint.x} ${endPoint.y}`).fill('none').stroke({
                    width: width,
                    color: color
                });
            }
            if (start !== originalStart) {
                const arrowOffsetStart = {
                    x: (startPoint.x + correlation * controlPoint1.x) / (1 + correlation),
                    y: (startPoint.y + correlation * controlPoint1.y) / (1 + correlation)
                };
                arrowBranch1 = drawDirected.line(startPoint.x, startPoint.y, arrowOffsetStart.x, arrowOffsetStart.y).stroke({
                    color: color,
                    width: width,
                }).rotate(20, startPoint.x, startPoint.y);
                arrowBranch2 = drawDirected.line(startPoint.x, startPoint.y, arrowOffsetStart.x, arrowOffsetStart.y).stroke({
                    color: color,
                    width: width,
                }).rotate(-20, startPoint.x, startPoint.y);
            } else {
                const arrowOffsetEnd = {
                    x: (endPoint.x + correlation * controlPoint1.x) / (1 + correlation),
                    y: (endPoint.y + correlation * controlPoint1.y) / (1 + correlation)
                };
                arrowBranch1 = drawDirected.line(endPoint.x, endPoint.y, arrowOffsetEnd.x, arrowOffsetEnd.y).stroke({
                    color: color,
                    width: width,
                }).rotate(20, endPoint.x, endPoint.y);
                arrowBranch2 = drawDirected.line(endPoint.x, endPoint.y, arrowOffsetEnd.x, arrowOffsetEnd.y).stroke({
                    color: color,
                    width: width,
                }).rotate(-20, endPoint.x, endPoint.y);
            }

            return [straightLine, arrowBranch1, arrowBranch2]
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

        drawWeightText(start, end, weightText, drawUndirected, color, fontWeight) {
            // const weight = this.wMatrix[start.peakNumber][end.peakNumber];

            drawUndirected.text(weightText).amove((start.cx + end.cx) / 2, (start.cy + end.cy) / 2 + ((this.fontSize / 1.5) * this.ptSize)).font({
                anchor: 'end',
                size: this.fontSize / 1.5,
                weight: fontWeight

            }).stroke({
                color: color
            });

        },

        drawUndirectedGraph() {
            this.drawUndirected = SVG().addTo('#undirectedGraph').size(this.windowWidth, this.windowWidth);

            this.drawPeaks(this.peaksCoordinates, this.drawUndirected);

            for (let i = 0; i < this.undirectedGraphMatrix.length; i++) {
                for (let j = 0; j < this.undirectedGraphMatrix.length; j++) {
                    const start = {
                        cx: this.peaksCoordinates[i].cx,
                        cy: this.peaksCoordinates[i].cy,
                        peakNumber: i,
                    }

                    const end = {
                        cx: this.peaksCoordinates[j].cx,
                        cy: this.peaksCoordinates[j].cy,
                        peakNumber: j,
                    }
                    if (this.undirectedGraphMatrix[i][j] === 1) {
                        if (i !== j) {
                            this.drawUndirectedLine(start, end, this.drawUndirected);
                            const weightText = this.wMatrix[start.peakNumber][end.peakNumber]
                            this.drawWeightText(start, end, weightText, this.drawUndirected, 'red', 1);
                        } else {
                            this.drawCurveBezier(start, this.drawUndirected);
                        }
                    }
                }
            }
        },

        setBMatrix() {
            let seedrandom = require('seedrandom');
            let random = seedrandom(parseInt(`${this.n1}${this.n2}${this.n3}${this.n4}`));

            const bMatrix = [];
            for (let i = 0; i < this.peaksNumber; i++) {
                bMatrix[i] = [];
                for (let j = 0; j < this.peaksNumber; j++) {
                    bMatrix[i][j] = (random() * 2.0);
                }
            }
            this.bMatrix = bMatrix;

        },

        setCMatrix() {
            const cMatrix = [];
            for (let i = 0; i < this.peaksNumber; i++) {
                cMatrix[i] = [];
                for (let j = 0; j < this.peaksNumber; j++) {
                    cMatrix[i][j] = Math.ceil(this.bMatrix[i][j] * 100 * this.undirectedGraphMatrix[i][j]);
                }
            }
            this.cMatrix = cMatrix;
        },

        setDMatrix() {
            const dMatrix = [];
            for (let i = 0; i < this.peaksNumber; i++) {
                dMatrix[i] = [];
                for (let j = 0; j < this.peaksNumber; j++) {
                    dMatrix[i][j] = this.cMatrix[i][j] > 0 ? 1 : 0;
                }
            }
            this.dMatrix = dMatrix;
        },

        setHMatrix() {
            const hMatrix = [];
            for (let i = 0; i < this.peaksNumber; i++) {
                hMatrix[i] = [];
                for (let j = 0; j < this.peaksNumber; j++) {
                    if (this.dMatrix[i][j] !== this.dMatrix[j][i]) {
                        hMatrix[i][j] = 1
                    } else {
                        hMatrix[i][j] = 0
                    }
                }
            }
            this.hMatrix = hMatrix;
        },

        setTMatrix() {
            const tMatrix = [];
            for (let i = 0; i < this.peaksNumber; i++) {
                tMatrix[i] = [];
                for (let j = 0; j < this.peaksNumber; j++) {
                    if (i < j) {
                        tMatrix[i][j] = 1
                    } else {
                        tMatrix[i][j] = 0
                    }
                }
            }
            this.tMatrix = tMatrix;
        },

        setWMatrix() {
            const wMatrix = JSON.parse(JSON.stringify(this.undirectedGraphMatrix));
            for (let i = 0; i < this.peaksNumber; i++) {
                for (let j = 0; j < this.peaksNumber; j++) {
                    const el = (this.dMatrix[i][j] + this.hMatrix[i][j] * this.tMatrix[i][j]) * this.cMatrix[i][j]
                    wMatrix[i][j] = el;
                    wMatrix[j][i] = el;

                }
            }
            this.wMatrix = wMatrix;
        },

        setWeightSum() {
            this.weightSum = this.primaSteps.getWeightSum()
        },

        prima() {
            const visited = new Array(this.peaksNumber).fill(false);
            this.primaSteps = new LinkedList();

            let start = 0;
            visited[start] = true;

            while (this.primaSteps.getData().length < this.peaksNumber - 1) {
                let minWeight = Infinity;
                let minStart = null;
                let minEnd = null;

                for (let i = 0; i < this.peaksNumber; i++) {
                    if (visited[i]) {
                        for (let j = 0; j < this.peaksNumber; j++) {
                            if (!visited[j] && this.wMatrix[i][j] && this.wMatrix[i][j] < minWeight) {
                                minWeight = this.wMatrix[i][j];
                                minStart = i;
                                minEnd = j;
                            }
                        }
                    }
                }

                if (minStart === null || minEnd === null) {
                    break;
                }

                let peakStatuses = Array(this.peaksNumber).fill('new');
                for (let j = 0; j < this.peaksNumber; j++) {
                    if (visited[j]) {
                        peakStatuses[j] = 'visited';
                    }
                }
                peakStatuses[minStart] = 'active';

                const weight = this.wMatrix[minStart][minEnd];
                peakStatuses[minEnd] = 'visited';
                this.primaSteps.add({step: [minStart, minEnd, weight], peakStatuses: peakStatuses, drawElements: []});

                start = minEnd;
                visited[minEnd] = true;
            }

            this.primaSteps.add({
                step: [0, 0, 0],
                peakStatuses: Array(this.peaksNumber).fill('visited'),
                drawElements: []
            });
            this.primaStep = this.primaSteps.head;

            this.primaStepsToDisplay = this.primaSteps.getStepsToDisplay();
        },

        primaDoNextStep() {
            const primaSteps = this.primaSteps.getData()


            for (let i = 0; i < this.peaksNumber; i++) {
                let peakCoordinate = {
                    cx: this.peaksCoordinates[i].cx,
                    cy: this.peaksCoordinates[i].cy,
                    title: this.peaksCoordinates[i].title
                }

                let color = 'red';
                if (primaSteps[this.stepNumber].data.peakStatuses[i] === 'visited') {
                    color = 'green';
                } else if (primaSteps[this.stepNumber].data.peakStatuses[i] === 'active') {
                    color = 'orange';
                }

                this.drawPeak(peakCoordinate, this.drawUndirected, color, 3)

            }

            const start = {
                cx: this.peaksCoordinates[primaSteps[this.stepNumber].data.step[0]].cx,
                cy: this.peaksCoordinates[primaSteps[this.stepNumber].data.step[0]].cy,
                peakNumber: primaSteps[this.stepNumber].data.step[0]

            }
            const end = {
                cx: this.peaksCoordinates[primaSteps[this.stepNumber].data.step[1]].cx,
                cy: this.peaksCoordinates[primaSteps[this.stepNumber].data.step[1]].cy,
                peakNumber: primaSteps[this.stepNumber].data.step[1]
            }

            if (primaSteps[this.stepNumber].data.step[0] !== primaSteps[this.stepNumber].data.step[1]) {
                primaSteps[this.stepNumber].data.drawElements = this.drawDirectedLine(start, end, this.drawUndirected, 'red', 3, false);
                const weightText = primaSteps[this.stepNumber].data.step[2]
                this.drawWeightText(start, end, weightText, this.drawUndirected, 'green', 500);
            }

            if (this.primaStep.next !== null) {
                this.primaStep = this.primaStep.next;
                this.stepNumber++;
            }


        },

        primaDoReset() {
            this.drawUndirected.remove()
            this.drawUndirectedGraph()
            this.stepNumber = 0;
            this.primaStep = this.primaSteps.head
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
            this.setBMatrix()
            this.setCMatrix()
            this.setDMatrix()
            this.setHMatrix()
            this.setTMatrix()
            this.setWMatrix()
        }
    }
}
</script>
