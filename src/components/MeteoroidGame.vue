<template>
  <canvas ref="canvas" class="meteoroid-canvas"></canvas>
</template>

<script lang="ts">
import Vue from "vue";

interface Point {
  x: number;
  y: number;
}

interface InternalDetail {
  type: "line";
  p1: Point;
  p2: Point;
}

interface Meteoroid {
  id: number;
  x: number;
  y: number;
  vx: number;
  vy: number;
  angle: number;
  rotSpeed: number;
  baseRadius: number;
  collisionRadius: number;
  points: Point[];
  details: InternalDetail[];
  hovered: boolean;
}

interface Shard {
  x: number;
  y: number;
  vx: number;
  vy: number;
  angle: number;
  rotSpeed: number;
  length: number;
  life: number;
  maxLife: number;
}

interface Spark {
  x: number;
  y: number;
  vx: number;
  vy: number;
  size: number;
  life: number;
  maxLife: number;
}

export default Vue.extend({
  name: "MeteoroidGame",
  data() {
    return {
      meteoroids: [] as Meteoroid[],
      shards: [] as Shard[],
      sparks: [] as Spark[],
      animationFrameId: 0,
      width: 0,
      height: 0,
      maxMeteoroids: 5,
      respawnTimers: [] as number[],
    };
  },
  mounted() {
    this.initCanvas();
    window.addEventListener("resize", this.handleResize);
    window.addEventListener("pointerdown", this.handlePointerDown);
    window.addEventListener("pointermove", this.handlePointerMove);
  },
  beforeDestroy() {
    cancelAnimationFrame(this.animationFrameId);
    window.removeEventListener("resize", this.handleResize);
    window.removeEventListener("pointerdown", this.handlePointerDown);
    window.removeEventListener("pointermove", this.handlePointerMove);
    this.respawnTimers.forEach(timer => clearTimeout(timer));
    document.body.style.cursor = "";
  },
  methods: {
    initCanvas() {
      const canvas = this.$refs.canvas as HTMLCanvasElement;
      if (!canvas) return;

      this.updateSize();
      this.meteoroids = [];
      this.shards = [];
      this.sparks = [];

      // Initial spawn: 4-5 asteroids spread across the screen
      const count = this.width < 768 ? 3 : 5;
      this.maxMeteoroids = count;
      for (let i = 0; i < count; i++) {
        this.meteoroids.push(this.createMeteoroid(false));
      }

      this.animate();
    },
    updateSize() {
      const canvas = this.$refs.canvas as HTMLCanvasElement;
      if (!canvas) return;
      this.width = window.innerWidth;
      this.height = window.innerHeight;
      const dpr = Math.min(window.devicePixelRatio || 1, 2);
      canvas.width = Math.floor(this.width * dpr);
      canvas.height = Math.floor(this.height * dpr);
      const ctx = canvas.getContext("2d");
      if (ctx) {
        ctx.scale(dpr, dpr);
      }
    },
    handleResize() {
      this.updateSize();
    },
    createMeteoroid(fromEdge = true): Meteoroid {
      // Base radius between 24px and 52px
      const baseRadius = 24 + Math.random() * 28;
      // 7 to 11 vertices for irregular jagged polygon
      const numVertices = Math.floor(7 + Math.random() * 5);
      const points: Point[] = [];
      let maxRadius = 0;

      for (let i = 0; i < numVertices; i++) {
        const baseAngle = (i / numVertices) * Math.PI * 2;
        // Jitter angle slightly
        const angle = baseAngle + (Math.random() - 0.5) * 0.35;
        // Jitter radius between 0.65x and 1.35x
        const r = baseRadius * (0.65 + Math.random() * 0.7);
        if (r > maxRadius) maxRadius = r;
        points.push({
          x: Math.cos(angle) * r,
          y: Math.sin(angle) * r,
        });
      }

      // Internal facet lines to give rich crater/rock texture
      const details: InternalDetail[] = [];
      const numDetails = Math.floor(1 + Math.random() * 2);
      for (let d = 0; d < numDetails; d++) {
        const i1 = Math.floor(Math.random() * numVertices);
        const i2 = (i1 + 2 + Math.floor(Math.random() * (numVertices - 4))) % numVertices;
        details.push({
          type: "line",
          p1: { x: points[i1].x * 0.45, y: points[i1].y * 0.45 },
          p2: { x: points[i2].x * 0.55, y: points[i2].y * 0.55 },
        });
      }

      let x = 0;
      let y = 0;
      let vx = 0;
      let vy = 0;
      const speed = 0.35 + Math.random() * 0.4;

      if (fromEdge) {
        const edge = Math.floor(Math.random() * 4); // 0: top, 1: right, 2: bottom, 3: left
        const pad = maxRadius + 15;
        if (edge === 0) {
          x = Math.random() * this.width;
          y = -pad;
          vx = (Math.random() - 0.5) * speed;
          vy = speed * (0.4 + Math.random() * 0.6);
        } else if (edge === 1) {
          x = this.width + pad;
          y = Math.random() * this.height;
          vx = -speed * (0.4 + Math.random() * 0.6);
          vy = (Math.random() - 0.5) * speed;
        } else if (edge === 2) {
          x = Math.random() * this.width;
          y = this.height + pad;
          vx = (Math.random() - 0.5) * speed;
          vy = -speed * (0.4 + Math.random() * 0.6);
        } else {
          x = -pad;
          y = Math.random() * this.height;
          vx = speed * (0.4 + Math.random() * 0.6);
          vy = (Math.random() - 0.5) * speed;
        }
      } else {
        // Initial spread
        x = Math.random() * this.width;
        y = Math.random() * this.height;
        const dir = Math.random() * Math.PI * 2;
        vx = Math.cos(dir) * speed;
        vy = Math.sin(dir) * speed;
      }

      return {
        id: Math.random(),
        x,
        y,
        vx,
        vy,
        angle: Math.random() * Math.PI * 2,
        rotSpeed: (Math.random() - 0.5) * 0.012,
        baseRadius,
        collisionRadius: maxRadius,
        points,
        details,
        hovered: false,
      };
    },
    handlePointerDown(e: MouseEvent | TouchEvent) {
      // If modal overlay is active, let it handle the click
      if (document.querySelector(".overlay")) {
        return;
      }

      // If clicking interactive elements like links or buttons, do not trigger game click
      const target = e.target as HTMLElement | null;
      if (target && target.closest("a, button, input, textarea, select, [role='button'], .dialog, .project-link, .modal")) {
        return;
      }

      let clientX = 0;
      let clientY = 0;
      if ("clientX" in e) {
        clientX = e.clientX;
        clientY = e.clientY;
      } else if (e.touches && e.touches.length > 0) {
        clientX = e.touches[0].clientX;
        clientY = e.touches[0].clientY;
      } else {
        return;
      }

      // Check click against meteoroids
      for (let i = this.meteoroids.length - 1; i >= 0; i--) {
        const m = this.meteoroids[i];
        const dist = Math.hypot(clientX - m.x, clientY - m.y);
        if (dist <= m.collisionRadius * 1.35) {
          this.triggerExplosion(m);
          this.meteoroids.splice(i, 1);

          // Queue respawn after short cooldown
          const timer = window.setTimeout(() => {
            if (this.meteoroids.length < this.maxMeteoroids) {
              this.meteoroids.push(this.createMeteoroid(true));
            }
          }, 1800 + Math.random() * 1200);
          this.respawnTimers.push(timer);
          break;
        }
      }
    },
    handlePointerMove(e: MouseEvent) {
      const clientX = e.clientX;
      const clientY = e.clientY;
      let anyHovered = false;

      for (let i = 0; i < this.meteoroids.length; i++) {
        const m = this.meteoroids[i];
        const dist = Math.hypot(clientX - m.x, clientY - m.y);
        const isHovered = dist <= m.collisionRadius * 1.25;
        m.hovered = isHovered;
        if (isHovered) anyHovered = true;
      }

      const target = e.target as HTMLElement | null;
      const isInteractive = target && target.closest("a, button, input, textarea, select, [role='button'], .dialog, .overlay");
      if (anyHovered && !isInteractive) {
        document.body.style.cursor = "crosshair";
      } else {
        document.body.style.cursor = "";
      }
    },
    triggerExplosion(m: Meteoroid) {
      // 1. Line shards (fragments of the asteroid outline)
      const shardCount = 8 + Math.floor(Math.random() * 4);
      for (let i = 0; i < shardCount; i++) {
        const angle = (i / shardCount) * Math.PI * 2 + (Math.random() - 0.5) * 0.6;
        const speed = 1.8 + Math.random() * 3.2;
        const life = 35 + Math.floor(Math.random() * 15);
        this.shards.push({
          x: m.x,
          y: m.y,
          vx: Math.cos(angle) * speed + m.vx * 0.4,
          vy: Math.sin(angle) * speed + m.vy * 0.4,
          angle: Math.random() * Math.PI * 2,
          rotSpeed: (Math.random() - 0.5) * 0.2,
          length: 8 + Math.random() * 14,
          life,
          maxLife: life,
        });
      }

      // 2. Dust sparks flying outwards
      const sparkCount = 14 + Math.floor(Math.random() * 6);
      for (let i = 0; i < sparkCount; i++) {
        const angle = Math.random() * Math.PI * 2;
        const speed = 1.0 + Math.random() * 3.8;
        const life = 25 + Math.floor(Math.random() * 15);
        this.sparks.push({
          x: m.x,
          y: m.y,
          vx: Math.cos(angle) * speed,
          vy: Math.sin(angle) * speed,
          size: 1.2 + Math.random() * 1.8,
          life,
          maxLife: life,
        });
      }
    },
    animate() {
      const canvas = this.$refs.canvas as HTMLCanvasElement;
      if (!canvas) return;
      const ctx = canvas.getContext("2d");
      if (!ctx) return;

      ctx.clearRect(0, 0, this.width, this.height);

      // --- Update & Draw Meteoroids ---
      for (let i = 0; i < this.meteoroids.length; i++) {
        const m = this.meteoroids[i];
        m.x += m.vx;
        m.y += m.vy;
        m.angle += m.rotSpeed;

        // Screen boundary wrap with margin
        const pad = m.collisionRadius + 30;
        if (m.x < -pad) m.x = this.width + pad;
        else if (m.x > this.width + pad) m.x = -pad;
        if (m.y < -pad) m.y = this.height + pad;
        else if (m.y > this.height + pad) m.y = -pad;

        // Draw meteoroid outline in #dcdcdc
        ctx.save();
        ctx.translate(m.x, m.y);
        ctx.rotate(m.angle);

        // Highlight slightly when hovered
        const alpha = m.hovered ? 0.85 : 0.45;
        ctx.strokeStyle = `rgba(220, 220, 220, ${alpha})`;
        ctx.lineWidth = 1.5;
        ctx.lineJoin = "round";

        // Outer polygon
        ctx.beginPath();
        for (let p = 0; p < m.points.length; p++) {
          const pt = m.points[p];
          if (p === 0) ctx.moveTo(pt.x, pt.y);
          else ctx.lineTo(pt.x, pt.y);
        }
        ctx.closePath();
        ctx.stroke();

        // Inner facet details
        for (let d = 0; d < m.details.length; d++) {
          const dt = m.details[d];
          ctx.beginPath();
          ctx.moveTo(dt.p1.x, dt.p1.y);
          ctx.lineTo(dt.p2.x, dt.p2.y);
          ctx.stroke();
        }

        ctx.restore();
      }

      // --- Update & Draw Shards ---
      for (let s = this.shards.length - 1; s >= 0; s--) {
        const shard = this.shards[s];
        shard.x += shard.vx;
        shard.y += shard.vy;
        shard.vx *= 0.95; // drag
        shard.vy *= 0.95;
        shard.angle += shard.rotSpeed;
        shard.life--;

        if (shard.life <= 0) {
          this.shards.splice(s, 1);
          continue;
        }

        const alpha = (shard.life / shard.maxLife) * 0.9;
        ctx.save();
        ctx.translate(shard.x, shard.y);
        ctx.rotate(shard.angle);
        ctx.strokeStyle = `rgba(220, 220, 220, ${alpha})`;
        ctx.lineWidth = 1.5;
        ctx.beginPath();
        ctx.moveTo(-shard.length / 2, 0);
        ctx.lineTo(shard.length / 2, 0);
        ctx.stroke();
        ctx.restore();
      }

      // --- Update & Draw Sparks ---
      for (let p = this.sparks.length - 1; p >= 0; p--) {
        const spark = this.sparks[p];
        spark.x += spark.vx;
        spark.y += spark.vy;
        spark.vx *= 0.94;
        spark.vy *= 0.94;
        spark.life--;

        if (spark.life <= 0) {
          this.sparks.splice(p, 1);
          continue;
        }

        const alpha = (spark.life / spark.maxLife) * 0.85;
        ctx.fillStyle = `rgba(220, 220, 220, ${alpha})`;
        ctx.beginPath();
        ctx.arc(spark.x, spark.y, spark.size, 0, Math.PI * 2);
        ctx.fill();
      }

      this.animationFrameId = requestAnimationFrame(this.animate);
    },
  },
});
</script>

<style scoped>
.meteoroid-canvas {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  z-index: 0;
  pointer-events: none;
}
</style>
