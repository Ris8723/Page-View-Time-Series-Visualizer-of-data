# Page-View-Time-Series-Visualizer-of-data
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

# Load and clean data
def load_and_clean_data(filepath):
    # Load data
    df = pd.read_csv(filepath, parse_dates=["date"], index_col="date")
    
    # Clean data by filtering out top and bottom 2.5% of page views
    lower_bound = df["value"].quantile(0.025)
    upper_bound = df["value"].quantile(0.975)
    df = df[(df["value"] >= lower_bound) & (df["value"] <= upper_bound)]
    
    return df

# Draw line plot
def draw_line_plot(df):
    fig, ax = plt.subplots(figsize=(12, 6))
    ax.plot(df.index, df["value"], color="red", linewidth=1)
    ax.set_title("Daily freeCodeCamp Forum Page Views 5/2016-12/2019")
    ax.set_xlabel("Date")
    ax.set_ylabel("Page Views")
    fig.savefig("line_plot.png")
    return fig

# Draw bar plot
def draw_bar_plot(df):
    # Prepare data
    df_bar = df.copy()
    df_bar["year"] = df_bar.index.year
    df_bar["month"] = df_bar.index.month
    df_grouped = df_bar.groupby(["year", "month"])["value"].mean().unstack()
    
    # Plot data
    fig, ax = plt.subplots(figsize=(12, 6))
    df_grouped.plot(kind="bar", ax=ax)
    ax.set_xlabel("Years")
    ax.set_ylabel("Average Page Views")
    ax.set_title("Monthly Average Page Views")
    ax.legend(title="Months", labels=[
        'Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun',
        'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec'
    ])
    fig.savefig("bar_plot.png")
    return fig

# Draw box plot
def draw_box_plot(df):
    # Prepare data for box plots
    df_box = df.copy()
    df_box.reset_index(inplace=True)
    df_box["year"] = df_box["date"].dt.year
    df_box["month"] = df_box["date"].dt.strftime('%b')
    month_order = ['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun',
                   'Jul', 'Aug', 'Sep', 'Oct', 'Nov', 'Dec']
    
    # Create box plots
    fig, axes = plt.subplots(1, 2, figsize=(15, 6))
    
    # Year-wise box plot
    sns.boxplot(x="year", y="value", data=df_box, ax=axes[0])
    axes[0].set_title("Year-wise Box Plot (Trend)")
    axes[0].set_xlabel("Year")
    axes[0].set_ylabel("Page Views")
    
    # Month-wise box plot
    sns.boxplot(x="month", y="value", data=df_box, order=month_order, ax=axes[1])
    axes[1].set_title("Month-wise Box Plot (Seasonality)")
    axes[1].set_xlabel("Month")
    axes[1].set_ylabel("Page Views")
    
    fig.savefig("box_plot.png")
    return fig

# Main function
if __name__ == "__main__":
    # Load and clean data
    filepath = "fcc-forum-pageviews.csv"
    df = load_and_clean_data(filepath)
    
    # Generate visualizations
    draw_line_plot(df)
    draw_bar_plot(df)
    draw_box_plot(df)
