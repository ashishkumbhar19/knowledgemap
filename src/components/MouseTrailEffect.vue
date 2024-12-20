<!-- <template>
  <canvas ref="canvasRef" style="display: block; position: absolute; top: 0; left: 0; z-index: 9999;"></canvas>
</template>

<script>
import { ref, onMounted, onUnmounted } from 'vue';

export default {
  name: 'MouseTrailEffect',
  setup() {
    const canvasRef = ref(null);

    onMounted(() => {
      const canvas = canvasRef.value;
      const ctx = canvas.getContext('2d');
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;

      const particles = [];
      const maxParticles = 100;

      class Particle {
        constructor(x, y) {
          this.x = x;
          this.y = y;
          this.size = 5;
          this.alpha = 1;
        }

        draw() {
          ctx.beginPath();
          ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
          ctx.fillStyle = `rgba(255, 255, 255, ${this.alpha})`;
          ctx.fill();
        }

        update() {
          this.alpha -= 0.02;
          this.size -= 0.1;
        }
      }

      const handleMouseMove = (e) => {
        if (particles.length >= maxParticles) {
          particles.shift();
        }
        particles.push(new Particle(e.clientX, e.clientY));
      };

      const animateParticles = () => {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        particles.forEach((particle, index) => {
          particle.update();
          particle.draw();
          if (particle.alpha <= 0 || particle.size <= 0) {
            particles.splice(index, 1);
          }
        });
      };

      const animate = () => {
        animateParticles();
        requestAnimationFrame(animate);
      };

      canvas.addEventListener('mousemove', handleMouseMove);
      animate();

      onUnmounted(() => {
        canvas.removeEventListener('mousemove', handleMouseMove);
      });
    });

    return {
      canvasRef,
    };
  },
};
</script>

<style scoped>
canvas {
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 9999;
}
</style> -->
