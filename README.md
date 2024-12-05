# Load necessary libraries  
library(ggplot2)
library(gifski)

# Sample data for daily steps over 14 days
steps <- c(7348, 18176, 5277, 8539, 14012, 16744, 19564, 6668, 10488, 7652, 4894, 11284, 19697, 14989)
days <- 1:14

# Create a vector of weekdays including the weekend
weekdays <- rep(c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"), length.out = 14)

# Create a data frame with steps, days, and weekdays
data <- data.frame(days = days, steps = steps, weekdays = factor(weekdays, levels = c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday")))

# Create a directory to store the frames
dir.create("frames")

# Generate individual frames
for (i in 1:14) {
  # Filter data up to the ith day
  data_sub <- data[1:i, ]
  
  # Create the plot for the current day
  p <- ggplot(data_sub, aes(x = weekdays, y = steps)) +
    geom_point(color = "blue", size = 3) +
    geom_smooth(data = data, aes(x = weekdays, y = steps, group = 1), method = "lm", color = "red", se = FALSE) +
    labs(title = "Daily Steps Over 14 Days with Linear Regression",
         subtitle = paste("Day:", i), x = "Weekdays", y = "Steps") +
    ylim(min(steps), max(steps))
  
  # Save the current plot as a PNG file
  ggsave(filename = sprintf("frames/plot_%02d.png", i), plot = p, width = 6, height = 4, dpi = 150)
}

# Create a GIF from the PNG frames
png_files <- list.files("frames", pattern = "plot_\\d+.png", full.names = TRUE)
gifski(png_files, gif_file = "daily_steps_animation.gif", width = 600, height = 400, delay = 0.5)

# Clean up: remove the frames directory
unlink("frames", recursive = TRUE)

