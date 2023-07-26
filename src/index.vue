<template>
    <view>
        <canvas :id="props.id" :style="{ 'width': props.size + 'px', 'height': props.size + 'px' }" type="2d"></canvas>
    </view>
</template>
<script setup lang="ts">
import { onMounted, onUpdated, PropType } from 'vue';
import Taro from '@tarojs/taro';
import QR from './qrcodegen'

type Level = 'L' | 'M' | 'Q' | 'H';
type Modules = ReturnType<QR.QrCode['getModules']>;

const isH5 = !(Taro.getEnv() === 'WEAPP' || Taro.getEnv() === 'JD');
const ErrorCorrectLevelMap: Record<Level, QR.QrCode.Ecc> = {
    L: QR.QrCode.Ecc.LOW,
    M: QR.QrCode.Ecc.MEDIUM,
    Q: QR.QrCode.Ecc.QUARTILE,
    H: QR.QrCode.Ecc.HIGH,
}


const props = defineProps({
    value: {
        type: String,
        required: true,
        default: '',
    },
    size: {
        type: Number,
        default: 100,
    },
    level: {
        type: String as PropType<Level>,
        default: 'H',
    },
    background: {
        type: String,
        default: '#fff',
    },
    foreground: {
        type: String,
        default: '#000',
    },
    margin: {
        type: Number,
        required: false,
        default: 0,
    },
    id: {
        type: String,
        required: false,
        default: 'spcanvas' + new Date().getTime(),
    },
    timeout: {
        type: Number,
        required: false,
        default: !(Taro.getEnv() === 'WEAPP' || Taro.getEnv() === 'JD') ? 0 : 1000
    }
});

function generatePath(modules: Modules, margin: number = 0): string {
    const ops: string[] = []
    modules.forEach(function (row, y) {
        let start: number | null = null
        row.forEach(function (cell, x) {
            if (!cell && start !== null) {
                // M0 0h7v1H0z injects the space with the move and drops the comma,
                // saving a char per operation
                ops.push(
                    `M${start + margin} ${y + margin}h${x - start}v1H${start + margin}z`
                )
                start = null
                return
            }

            // end of row, clean up or skip
            if (x === row.length - 1) {
                if (!cell) {
                    // We would have closed the op above already so this can only mean
                    // 2+ light modules in a row.
                    return
                }
                if (start === null) {
                    // Just a single dark module.
                    ops.push(`M${x + margin},${y + margin} h1v1H${x + margin}z`)
                } else {
                    // Otherwise finish the current line.
                    ops.push(
                        `M${start + margin},${y + margin} h${x + 1 - start}v1H${start + margin
                        }z`
                    )
                }
                return
            }

            if (cell && start === null) {
                start = x
            }
        })
    })
    return ops.join('')
}

function createPath2D(canvas: HTMLCanvasElement | any, cells: any): Path2D {
    if (isH5) {
        return new Path2D(generatePath(cells, props.margin));
    } else {
        const path2d = canvas.createPath2D();
        cells.forEach((row, y) => {
            row.forEach((cell, x) => {
                if (cell) {
                    path2d.rect(x, y, 1, 1)
                }
            });
        });
        return path2d;
    }
}


function drawQrCode(canvas: HTMLCanvasElement) {
    const ctx = canvas.getContext('2d')
    if (!ctx) {
        return;
    }

    const cells = QR.QrCode.encodeText(props.value, ErrorCorrectLevelMap[props.level]).getModules()
    const numCells = cells.length + props.margin * 2;

    const devicePixelRatio = window.devicePixelRatio || 1;

    const scale = (props.size / numCells) * devicePixelRatio;
    canvas.height = canvas.width = props.size * devicePixelRatio;
    ctx.scale(scale, scale);
    ctx.fillStyle = props.background;
    ctx.fillRect(0, 0, numCells, numCells);
    ctx.fillStyle = props.foreground;
    ctx.fill(createPath2D(canvas, cells))
}


//wil export as event by emit
function renderCanvas() {
    setTimeout(() => {
        if (isH5) {
            drawQrCode(document.getElementById(props.id)?.childNodes[0] as HTMLCanvasElement)
        } else {
            Taro.createSelectorQuery()
                .select(`#${props.id}`)
                .fields({
                    node: true,
                    size: true
                })
                .exec((res) => {
                    let canvas = res[0].node;
                    drawQrCode(canvas);
                });
        }
    }, props.timeout)
}

onMounted(renderCanvas);
onUpdated(renderCanvas);
defineExpose({
    renderCanvas
});
</script>
