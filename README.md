# An Exploration of Machine Learning Techniques for Taxonomy

In this report we create a machine learning algorithm for classifying marine mammal vocalizations. The algorithm is based on building spectrograms of the vocalizations and utilizing the singular value decomposition to transform the data into the optimal basis for analysis. The results from using different unsupervised and supervised classifiers in the algorithm are compared. Sound clips from a number of different species of dolphins, whales, and pinnipeds (seal-like mammals) are used to evaluate the algorithm.

# Introduction and Overview

\indent Taxonomy, the science of classifying organisms, is vital to understanding the evolution of the world in which we live. Classically, biologists decide how plants and animals are related using their own inference-- but what if we could create an algorithm that classified animals with little (or even without \textit{any}) prior subjective knowledge? Equipped with time frequency analysis, machine learning, and data from the Watkins Marine Mammal Sound Database\footnote{https://cis.whoi.edu/science/B/whalesounds/index.cfm}, this is what we attempt to achieve. \\
\indent We focus on the case of marine mammals. We use twenty-five vocalization sound clips each from nine animals in three categories: pinnipeds (walrus, harp seal, ross seal), dolphins (atlantic spotted dolphin, white sided dolphin, and killer whale), and whales (humpback whale, sperm whale, and white beluga whale). We test whether our supervised machine learning algorithms classify each recording accurately, both within a category of animal (can it distinguish between types of whales, e.g.) and  whether the algorithms can classify the animals across all categories (can it decide if a sound clip belongs to a pinniped, and then specifically which species of pinniped, e.g.).\\

# Theoretical Background
Time Frequency Analysis}
        The main tool for analyzing frequency content is the Fourier transform, which decomposes a function into its respective frequencies. The absolute value of the Fourier transform represents the amount of each frequency that is present in the function. The Fourier transform and the inverse Fourier transform are given by
        \begin{align}
            F(k) &= \frac{1}{\sqrt{2\pi}} \int_{-\infty}^{\infty} e^{-ikx}f(x)dx, \\
            f(x) &= \frac{1}{\sqrt{2\pi}} \int_{-\infty}^{\infty} e^{ikx}F(k)dk,
        \end{align}
        where the $k$'s are the wavenumbers.
        
        The main drawback of using the Fourier transform is that the time information in the signal is lost, i.e. there is no way of knowing when in time a specific frequency was produced. For non-stationary signals, this is a problem. The Gábor transform aims to solve this problem by providing information in both time and frequency. The Gábor transform of a function $f$ is defined as
        \begin{align}
            \mathcal{G}[f](t,\,\omega) = \int_{-\infty}^{\infty} f(\tau)\Bar{g}(\tau -t)e^{-i\omega \tau}d\tau
        \end{align}
        where $g(\tau - t)$ is some filter (Gábor window), and $\Bar{g}$ its complex conjugate. One can think of this as the filter $g$ sliding across the time signal, resulting in only a filtered part of the function $f$ being Fourier transformed at each instant. The frequency content can thus be traced back to a specific time window in the signal.
        
        When using the Gábor transform numerically, one chooses a filter, and lets the filter ``slide'' over the signal using a finite number of translations of the filter. For each location of the filter, the Fast Fourier Transform is applied to get the frequency content in this signal window. The frequency content of the entire signal can then be plotted as a spectrogram, where it is visualized as a function of both time and frequencies.
        
        A basic Gaussian filter is given by
        \begin{align}
            F(t) = e^{-a(t-\tau)^2},
        \end{align}
        where the parameter $a$ determines the width of the Gaussian and $\tau$ is the translation in $t$.
        
        One important aspect to keep in mind is that increased time resolution, i.e. using a narrower Gábor window when filtering, results in decreased frequency resolution, and vice versa. When using narrow time windows, low frequencies will not be picked up as their wavelengths are longer than the window width. When using wide windows, lower frequencies will be captured, but, since the time window is wide, it is impossible to tell exactly when in time the frequencies originated.
