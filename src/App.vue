<template>
  <canvas ref="el" width="600" height="600" border />
</template>

<script setup lang="ts">
import { $computed } from 'vue/macros'

const el = $ref<HTMLCanvasElement>()
const ctx = $computed(() => el!.getContext('2d')!)

// console.log('ctx', ctx)

const WIDTH = 600
const HEIGHT = 600

interface Point {
  x: number
  y: number
}

interface Branch {
  start: Point
  length: number
  // 角度
  theta: number
}

const pendingTasks: Function[] = []

function init() {
  ctx.strokeStyle = '#fff'

  // 开始的第一个支
  setp({
    start: { x: WIDTH / 2, y: HEIGHT },
    length: 20,
    theta: -Math.PI / 2,
  })
}

function setp(b: Branch, depth = 0) {
  const end = getEndPoint(b)
  drawBranch(b)

  // 50% 几率长左边支
  if (depth < 5 || Math.random() < 0.5) {
    // 先缓存起来，优先广度生长
    pendingTasks.push(() => {
    // leftBranch
      return setp({
        start: end,
        length: b.length + (Math.random() * 10 - 5),
        theta: b.theta - 0.3 * Math.random(),
      }, depth + 1)
    })
  }
  // 50% 几率长右边支
  if (depth < 5 || Math.random() < 0.5) {
    // 先缓存起来，优先广度生长
    pendingTasks.push(() => {
      // rightBranch
      return setp({
        start: end,
        length: b.length + (Math.random() * 10 - 5),
        theta: b.theta + 0.3 * Math.random(),
      }, depth + 1)
    })
  }
}
function frame() {
  const tasks = [...pendingTasks]
  pendingTasks.length = 0
  tasks.forEach(fn => fn())
}

let frameCount = 0
function startFrame() {
  requestAnimationFrame(() => {
    frameCount += 1
    if (frameCount % 3 === 0)
      frame()
    startFrame()
  })
}

startFrame()

function lineTo(p1: Point, p2: Point) {
  ctx.beginPath()
  ctx.moveTo(p1.x, p1.y)
  ctx.lineTo(p2.x, p2.y)
  ctx.stroke()
}

function getEndPoint(b: Branch) {
  const {
    start,
    length,
    theta,
  } = b
  return {
    x: start.x + length * Math.cos(theta), // 三角函数计算
    y: start.y + length * Math.sin(theta),
  }
}

function drawBranch(b: Branch) {
  const {
    start,
    length,
    theta,
  } = b
  lineTo(start, getEndPoint(b))
}

onMounted(() => {
  init()
})
</script>
