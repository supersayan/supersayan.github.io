<script lang="ts">
	import Delaunator from 'delaunator';
	import { onMount } from 'svelte';
	import { gsap } from 'gsap';

	// canvas setting
	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D | null;

	let mousePosition = [0, 0];

	onMount(() => {
		if (canvas === null) return;
		canvas.width = window.innerWidth;
		canvas.height = window.innerHeight;
		ctx = canvas.getContext('2d');

		createTriangles(canvas.width > canvas.height ? canvas.width : canvas.height);
		requestAnimationFrame(render);

		window.addEventListener('mousemove', function (e) {
			if (canvas === null) return;
			let r = canvas.getBoundingClientRect();

			mousePosition[0] = e.clientX - r.left;
			mousePosition[1] = e.clientY - r.top;
		});

		window.addEventListener('resize', function () {
			if (canvas === null) return;
			canvas.width = window.innerWidth;
			canvas.height = window.innerHeight;
			createTriangles(canvas.width > canvas.height ? canvas.width : canvas.height);
		});
	});

	type Point = {
		x: number;
		y: number;
		offset: {
			x: number;
			y: number;
		};
	};

	let vertices: Point[];
	let delaunay;
	let indices;
	let fragments: Fragment[];

	function createTriangles(viewWidth: number) {
		let size = 64,
			count = (viewWidth + 100) / size;

		vertices = [];
		for (let i = 0; i <= count; i++) {
			for (let j = 0; j <= count; j++) {
				vertices.push({
					x: i * size + randomRange(-size / 3, size / 3),
					y: j * size + randomRange(-size / 3, size / 3),
					offset: { x: 0, y: 0 }
				});
			}
		}

		delaunay = Delaunator.from(vertices.map((v) => [v.x, v.y]));
		indices = delaunay.triangles;

		fragments = [];

		for (let i = 0; i < indices.length; i += 3) {
			fragments.push(
				new Fragment(vertices[indices[i + 0]], vertices[indices[i + 1]], vertices[indices[i + 2]])
			);
			fragments[i / 3].setColors();
		}

		for (let i = 0; i < vertices.length; i++) {
			let offset = vertices[i].offset;

			let minX = 2,
				maxX = 6,
				minY = 2,
				maxY = 6;

			let y0 = randomRange(-maxY, -minY),
				y1 = randomRange(minY, maxY),
				x0 = randomRange(-maxX, -minX),
				x1 = randomRange(minX, maxX);

			offset.x = x0;
			offset.y = y0;

			gsap.fromTo(
				offset,
				{ y: y0 },
				{ y: y1, repeat: -1, yoyo: true, ease: 'Cubic.easeInOut', duration: randomRange(3, 6) }
			);
			gsap.fromTo(
				offset,
				{ x: x0 },
				{ x: x1, repeat: -1, yoyo: true, ease: 'Cubic.easeInOut', duration: randomRange(3, 6) }
			);
		}
	}

	function render() {
		draw();
		requestAnimationFrame(render);
	}

	function draw() {
		if (canvas === null || ctx === null) return;
		ctx.clearRect(0, 0, canvas.width, canvas.height);

		fragments.forEach(function (f) {
			f.draw();
		});
	}

	class Fragment {
		v0: Point;
		v1: Point;
		v2: Point;

		mouseOver = false;
		visible = true;

		fill = '';
		stroke = '';

		constructor(v0: Point, v1: Point, v2: Point) {
			this.v0 = v0;
			this.v1 = v1;
			this.v2 = v2;
		}

		setColors() {
			let h = 0;
			let s = '0%';
			let l1 = ~~randomRange(0, 10) + '%';

			this.fill = 'hsl(' + [h, s, l1].join(',') + ')';
		}
		draw() {
			if (ctx === null || this.visible === false) return;

			ctx.strokeStyle = this.fill;
			ctx.fillStyle = this.fill;

			ctx.beginPath();
			ctx.moveTo(this.v0.x + this.v0.offset.x, this.v0.y + this.v0.offset.y);
			ctx.lineTo(this.v1.x + this.v1.offset.x, this.v1.y + this.v1.offset.y);
			ctx.lineTo(this.v2.x + this.v2.offset.x, this.v2.y + this.v2.offset.y);
			ctx.closePath();
			ctx.stroke();
			ctx.fill();
		}
	}

	function randomRange(min: number, max: number) {
		return min + Math.random() * (max - min);
	}
</script>

<canvas id="anibg" bind:this={canvas}></canvas>

<style>
	#anibg {
		position: absolute;
		margin: auto;
		width: 100vw;
		max-width: 100%;
		height: 100vh;
		top: 0;
		bottom: 0;
		left: 0;
		right: 0;
		background-color: #121212;
		z-index: 0;
	}
</style>
