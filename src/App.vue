<template>
  <canvas ref="el" width="300" height="300" border />
</template>

<script setup lang="ts">
import { $computed } from 'vue/macros'

const el = $ref<HTMLCanvasElement>()
const ctx = $computed(() => el!.getContext('2d')!)

const WIDTH = 300
const HEIGHT = 300

interface Point{
  x: number
  y: number
}

interface Line {
  start: Point
  length: number
  // 角度
  theta: number
}

function init() {
  ctx.strokeStyle = '#fff'
  const startPoint = { x: WIDTH / 2, y: HEIGHT }
  const endPoint = { x: WIDTH / 2, y: HEIGHT / 2 }
  line(startPoint, endPoint)
  line(endPoint, { x: WIDTH / 2 + 50, y: 10 })
}

function lineTo(p1: Point, p2: Point) {
  ctx.beginPath()
  ctx.moveTo(p1.x, p1.y)
  ctx.lineTo(p2.x, p2.y)
  ctx.stroke()
}

function line(l: Line) {
  const { start, length, theta } = l
  const end = {
    x: start.x + length * Math.cos(theta),
    y: start.y + length * Math.sin(theta),
  }
  lineTo(start, end)
}

onMounted(() => {
  init()
})
</script>
