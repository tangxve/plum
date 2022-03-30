<template>
  <canvas ref="el" width="300" height="300" border />
</template>

<script setup lang="ts">
import { $computed } from 'vue/macros'

const el = $ref<HTMLCanvasElement>()
const ctx = $computed(() => el!.getContext('2d')!)

const WIDTH = 300
const HEIGHT = 300

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

  const branch = {
    start: {
      x: WIDTH / 2,
      y: HEIGHT,
    },
    length: 100,
    theta: -Math.PI / 2,
  }

  const end = getEndPoint(branch)
  drawBranch(branch)

  const leftBranch = {
    start: end,
    length: 100,
    theta: branch.theta - 0.1,
  }

  drawBranch(leftBranch)

  const rightBranch = {
    start: end,
    length: 100,
    theta: branch.theta + 0.1,
  }

  drawBranch(rightBranch)
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
