FROM nginx

# Remove default nginx website, doesn't remove other website files.
RUN rm -rf /usr/share/nginx/html/*

# For this project angulaar_shield was build with `ng build --out-path=angular_shield_build --env=production`
# You can also set a builder for the project (cf angular exemple)
COPY angular_shield_build /usr/share/nginx/html
