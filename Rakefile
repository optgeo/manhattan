extent = 'extent/extent.geojson'
src = 'us-northeast-latest.osm.pbf'

desc 'download data'
task :download do
  sh "curl -C - -O https://download.geofabrik.de/north-america/#{src}"
end

desc 'cut data'
task :cut do
  sh "osmium extract --polygon #{extent} --output src/src.pbf #{src}"
end

desc 'build tiles from source data using naru'
task :tiles do
  sh "osmium export --config ../naru/osmium-export-config.json --index-type=sparse_file_array --output-format=geojsonseq --output=- src/src.pbf | node ../naru/filter.js | tippecanoe --no-feature-limit --no-tile-size-limit --force --simplification=2 --maximum-zoom=15 --base-zoom=15 --hilbert --output=tiles.mbtiles"
  sh "tile-join --force --no-tile-compression --output-to-directory=docs/zxy --no-tile-size-limit tiles.mbtiles"
end

desc 'build style.json from HOCON descriptions'
task :style do
  sh 'parse-hocon hocon/style.conf > docs/style.json'
  sh 'gl-style-validate docs/style.json'
end

desc 'host the site for testing'
task :host do
  sh 'budo -d docs'
end

desc 'port-forward by serveo'
task :forward do
  sh 'autossh -M 0 -R vt-manhattan:443:localhost:9966 serveo.net'
end

