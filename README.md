import pandas as pd
import numpy as np

def optimize_data_types(df):
    # Get the original data types of the dataframe
    orig_dtypes = df.dtypes

    # Define a dictionary to store the mapping of columns and their optimized data types
    optimized_dtypes = {}

    # Loop through each column and analyze the data type
    for column in df.columns:
        col_data = df[column]
        col_dtype = col_data.dtype

        # Analyze the data type based on the maximum value in the column
        if col_dtype == np.int64:
            max_value = col_data.max()
            if max_value <= np.iinfo(np.int8).max:
                optimized_dtypes[column] = np.int8
            elif max_value <= np.iinfo(np.int16).max:
                optimized_dtypes[column] = np.int16
            elif max_value <= np.iinfo(np.int32).max:
                optimized_dtypes[column] = np.int32
            else:
                optimized_dtypes[column] = np.int64
        elif col_dtype == np.float64:
            max_value = col_data.max()
            min_value = col_data.min()
            if (max_value <= np.finfo(np.float16).max and
                    min_value >= np.finfo(np.float16).min):
                optimized_dtypes[column] = np.float16
            elif (max_value <= np.finfo(np.float32).max and
                    min_value >= np.finfo(np.float32).min):
                optimized_dtypes[column] = np.float32
            else:
                optimized_dtypes[column] = np.float64
        else:
            optimized_dtypes[column] = col_dtype

    # Convert the dataframe to the optimized data types
    df_optimized = df.astype(optimized_dtypes)

    return df_optimized

# Example usage:
# df = pd.read_csv('your_data_file.csv')
# df_optimized = optimize_data_types(df)
