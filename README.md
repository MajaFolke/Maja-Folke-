[README.md](https://github.com/user-attachments/files/34174156/README.md)[Uploading R[index.html](https://github.com/user-attachments/files/34174344/index.html)EADME.md…]()






<!doctype html>
<html>
<head>
<meta charset="utf-8">
<title>Video Grid</title>
<style>
  html, body { margin: 0; height: 100%; background: #000; overflow: hidden; }
  #grid {
    display: grid;
    /* columns/rows are set from COLUMNS and ROWS in the script below */
    width: 50vw;
    height: 50vh;
  }
  video { width: 50%; height: 50%; min-width: 0; min-height: 0; object-fit: cover; display: block; }
</style>
</head>
<body>
<div id="grid"></div>
<script>
  // ===== Grid size — change these to resize the grid =====
  // Number of videos side by side (left to right)
  const COLUMNS = 5;
  // Number of videos stacked (top to bottom)
  const ROWS = 5;
  // Total cells = COLUMNS * ROWS. If there are fewer videos than cells,
  // the remaining cells repeat the last video.

  // ===== Video files =====
  // Videos must be named 1.mp4, 2.mp4, 3.mp4 ... inside the videos/ folder.
  // The page loads them in number order and stops at the first missing number.
  const FOLDER = 'videos/';
  const EXTENSION = '.mp4';

  // ===== Order =====
  // false = number order (1, 2, 3 ...)
  // true  = every cell picks a random video on each page load (repeats allowed)
  const RANDOM = true;

  // Resolves true if the video file exists and can be loaded, false otherwise
  function videoExists(src) {
    return new Promise(resolve => {
      const probe = document.createElement('video');
      probe.preload = 'metadata';
      probe.onloadedmetadata = () => resolve(true);
      probe.onerror = () => resolve(false);
      probe.src = src;
    });
  }

  // Finds 1.mp4, 2.mp4, ... until one is missing.
  // In number order, it stops once every cell has a video; when RANDOM, it finds them all.
  async function findVideos() {
    const found = [];
    for (let n = 1; RANDOM || n <= COLUMNS * ROWS; n++) {
      const src = FOLDER + n + EXTENSION;
      if (!(await videoExists(src))) break;
      found.push(src);
    }
    return found;
  }

  findVideos().then(videos => {
    const grid = document.getElementById('grid');
    if (!videos.length) {
      grid.outerHTML = `<p style="color:#fff;font-family:sans-serif;padding:16px">No videos found. Name them ${FOLDER}1${EXTENSION}, ${FOLDER}2${EXTENSION}, ...</p>`;
      return;
    }
    // Sizes cells in whole pixels so no thin black seams appear between them.
    // Leftover pixels (when the screen doesn't divide evenly) go to the last row/column.
    function layout() {
      const colWidth = Math.floor(window.innerWidth / COLUMNS);
      const rowHeight = Math.floor(window.innerHeight / ROWS);
      grid.style.gridTemplateColumns = `${colWidth}px `.repeat(COLUMNS - 1) + 'minmax(0, 1fr)';
      grid.style.gridTemplateRows = `${rowHeight}px `.repeat(ROWS - 1) + 'minmax(0, 1fr)';
    }
    layout();
    window.addEventListener('resize', layout);

    for (let i = 0; i < COLUMNS * ROWS; i++) {
      const v = document.createElement('video');
      // RANDOM: each cell picks its own random video (the same video can appear in several cells).
      // Otherwise: number order, and leftover cells repeat the last video.
      v.src = RANDOM
        ? videos[Math.floor(Math.random() * videos.length)]
        : videos[Math.min(i, videos.length - 1)];
      v.autoplay = v.muted = v.loop = v.playsInline = true;
      grid.appendChild(v);
    }
  });
</script>
</body>
</html>





