<template>
  <canvas ref="el" width="600" height="600" border />
</template>

<script setup lang="ts">
import { $computed } from 'vue/macros'

const el = $ref<HTMLCanvasElement>()
const ctx = $computed(() => el!.getContext('2d')!)

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

function init() {
  ctx.strokeStyle = '#fff'

  // 开始的第一个支
  setp({
    start: { x: WIDTH / 2, y: HEIGHT },
    length: 30,
    theta: -Math.PI / 2,
  })
}

function setp(b: Branch) {
  const end = getEndPoint(b)
  drawBranch(b)

  // 50% 几率长左边支
  if (Math.random() < 0.5) {
    // leftBranch
    setp({
      start: end,
      length: b.length,
      theta: b.theta - 0.2,
    })
  }
  // 50% 几率长右边支
  if (Math.random() < 0.5) {
    // rightBranch
    setp({
      start: end,
      length: b.length,
      theta: b.theta + 0.2,
    })
  }
}

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
